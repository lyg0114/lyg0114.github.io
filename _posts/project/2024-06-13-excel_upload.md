---
title: "WebSocket, JdbcTemplate을 활용한 엑셀업로드기능을 구현"
date: 2024-06-13 08:40:00 +09:00
categories: [ project ]
tags: [ websocket, os, network, tcp/ip, osi 7 layer, 네트워크, JdbcTemplate ]
---

[사이드 프로젝트](https://github.com/lyg0114/install)의 엑셀 일괄 업로드 기능을 구현하며 정리한 내용임.

### 구현에 고려할 부분

1. #### 업로드 진행 상황 파악이 가능해야함.
2. #### 작업 속도가 빨라야 함.
3. #### 사용자에게 상황에 맞는 적절한 예외처리를 해야함.
 
## 1. 업로드 진행상황 표시
- 서버측에서 작업 진행상황 데이터를 클라이언트측에 전달할 수 있어야함.
- 일반적인 `http` 방식으로는 해결하기 어렵기에 [WebSocket](https://en.wikipedia.org/wiki/WebSocket)을 도입. 
  - 스프링에서도 역시 WebSocket 지원하는 [프로젝트](https://spring.io/guides/gs/messaging-stomp-websocket)가 있음.

### WebSocket 설정 클래스

```java
@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

	// WebSocket 핸들러를 등록하는 메서드
	@Override
	public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
		// "/progress/**" 경로에 ProgressWebSocketHandler를 추가
		// 클라이언트측에서 ** 부분에 sessionId를 추가하여 연결 요청
		registry.addHandler(progressWebSocketHandler(), "/progress/**");
	}

	// ProgressWebSocketHandler 빈(Bean)을 생성하는 메서드
	@Bean
	public ProgressWebSocketHandler progressWebSocketHandler() {
		return new ProgressWebSocketHandler();
	}
}

```

### WebSocket 핸들러 클래스

1. #### ConcurrentHashMap<String, WebSocketSession> sessions
  - 초기 형태는 `ConcurrentHashMap이` 아닌 `WebSocketSession session` 하나만 필드로 가지고 있었다.
  - 이경우 `ProgressWebSocketHandler` 클래스는 **Bean**으로 등록 되어 있기 때문에 여러 사용자가 동시에 엑셀 업로드를 진행하면 **동시성 문제**가 발생함.
    - `sessionID`와 `ConcurrentHashMap을` 통해 여러 세션을 관리할 수 있도록 처리

2. #### sendProgressUpdate()
  - 클라이언트 측으로 엑셀 업로드 진행상황 데이터를 전송하는 메서드.

```java
@Slf4j
public class ProgressWebSocketHandler extends TextWebSocketHandler {

	// WebSocket 세션을 저장할 ConcurrentHashMap
	private final ConcurrentHashMap<String, WebSocketSession> sessions = new ConcurrentHashMap<>();

	// 새로운 WebSocket 연결이 성립되었을 때 호출되는 메서드
	@Override
	public void afterConnectionEstablished(WebSocketSession session) {
		// 세션 ID를 추출하여 sessions 맵에 추가
		String sessionId = requireNonNull(session.getUri()).getPath().split("/progress/")[1];
		sessions.put(sessionId, session);
		log.info("Connection established with session ID: {}", sessionId);
	}

	// WebSocket 연결이 종료되었을 때 호출되는 메서드
	@Override
	public void afterConnectionClosed(WebSocketSession session, CloseStatus status) {
		// 세션 ID를 추출하여 sessions 맵에서 제거
		String sessionId = requireNonNull(session.getUri()).getPath().split("/progress/")[1];
		sessions.remove(sessionId);
		log.info("Connection closed for session ID: {}", sessionId);
	}

	// 클라이언트에게 진행 상황을 업데이트하는 메서드
	public void sendProgressUpdate(String sessionId, String message) throws IOException {
		WebSocketSession session = sessions.get(sessionId);
		// 세션이 존재하고 열려있을 경우 메시지 전송
		if (session != null && session.isOpen()) {
			session.sendMessage(new TextMessage(message));
		}
	}

	// 특정 세션을 종료하는 메서드
	public void closeSession(String sessionId) throws IOException {
		WebSocketSession session = sessions.get(sessionId);
		// 세션이 존재하고 열려있을 경우 세션 종료
		if (session != null && session.isOpen()) {
			session.close();
			log.info("Session closed for session ID: {}", sessionId);
		}
	}
}

```

### 시퀀스 다이어그램

<img src="/assets/img/websocket-flow.jpg" alt="x609" width="600">

- 클라이언트는 서버에 엑셀 파일을 전달함과 동시에 **sessionId**를 요청후 **sessionId**를 기반으로 **WebSocket**연결
- **connect success** 후 서버측에서 엑셀 업로드 현황데이터를 클라이어트측으로 전송한다.
- 모든 작업이 완료되면 서버측에서 `session.close()` 메서드를호출하여 **WebSocket** 연결을 종료한다.

### 클라이어트 코드

```javascript
$('#send').on('click', function (event) {
  event.preventDefault();
  var fileInput = $('#file-input')[0];
  if (fileInput.files.length === 0) {
    alert("Please select a file!");
    return;
  }

  var formData = new FormData();
  formData.append('file', fileInput.files[0]);
            // formData 에 엑셀 파일 셋팅

  $.ajax({
    url: 'http://localhost:8080/api/modems/v1/excel',
    type: 'POST',
    data: formData,
            // sessionId 요청 및 엑셀 파일 업로드
    contentType: false,
    processData: false,
    success: function (sessionId) {
      var socket = new WebSocket("ws://localhost:8080/progress/" + sessionId);
                    // 반환된 sessionId를 기반으로 websocket 연결 시도
      socket.onmessage = function (event) {
                    // 서버로부터 전달된 업로드 진행상황 데이터를 화면에 랜더링
        var current = parseInt(event.data, 10);
        $('#progress').text("progress : " + current + "%");
        if (current >= 100) {
          $('#complete').text('Upload successful!');
        }
      };
      socket.onopen = function () {
        console.log("WebSocket connection opened.");
      };
      socket.onclose = function () {
        console.log("WebSocket connection closed.");
      };
      socket.onerror = function (error) {
        console.log("WebSocket error: " + error);
      };
    },
    error: function () {
      $('#progress').text('Upload failed!');
    }
  });
});


```

### 서버 코드

```java
@Slf4j
@RequiredArgsConstructor
@RequestMapping("/api/modems/v1")
@RestController
public class ModemApiController {

	private final ModemService modemService;
	private final ModemExcelService modemExcelService;

	/**
	 * - 단말기 일괄 엑셀 등록
	 */
	@PostMapping("/excel")
	public ResponseEntity<String> addModemsByExcel(@RequestParam("file") MultipartFile file) {
		String sessionId = UUID.randomUUID().toString();
		        // UUID로 sessionId 생성
		new Thread(() -> {
			modemExcelService.uploadModemExcel(file, sessionId);
		}).start();

		return ResponseEntity
			.ok(sessionId);
	}
}
```

이로서 사용자는 업로드 진행 상황 파악이 가능.


## 2. 데이터 일괄 저장 (Bulk Insert)
- **spring-data-jpa**를 사용하면 쉽게 insert를 구현할 수 있다. 
- 문제점 : **느리다** 
  - for-loop를 통해 매번 INSERT 쿼리 요청 (매 요청마다 네트워크 자원 사용)

<img src="/assets/img/bulk_insert_time.jpg" alt="x609" width="600">

- 해결 : JdbcTemplate 사용 
- `JdbcTemplate.batchUpdate()`를 사용했을때와 `saveAll()`를 사용했을때 시간차이다 거의 **4배** 정도가 난다. 
- `JdbcTemplate.batchUpdate()`는 단건으로 매번 insert 쿼리를 전송하는것이 아니라 메모리에 일정양의 데이터가 쌓이면 한번에 insert 쿼리를 전송한다. 따라서 네트워크 요청도 획기적으로 줄어든다.

### JdbcTemplate 
```java
public void bulkInsertModem(List<ModemDto.ModemRequest> requests) {
  String sql = "INSERT INTO modem "
     + "(created_at, modem_no, imei, build_company, modem_status_cd, modem_type_cd) "
     + "VALUES (?,?,?,?,?,?)";

  LocalDateTime now = LocalDateTime.now();
  jdbcTemplate.batchUpdate(sql, requests, requests.size(), (ps, modemRequest) -> {
     ps.setString(1, now.toString());
     ps.setString(2, modemRequest.getModemNo());
     ps.setString(3, modemRequest.getImei());
     ps.setString(4, modemRequest.getBuildCompany());
     ps.setString(5, modemRequest.getModemStatusCd());
     ps.setString(6, modemRequest.getModemTypeCd());
  });
}
```
- 추후 데이터의 양이 많아지면 batchSize를 적절하게 조절 필요.

### SpringDataJpa
- 작성해야할 코드는 훨씬 간결하지만, 성능이 매우 아쉬운 받식
```java
	@Test
	void hibernate_벌크연산테스트() {
		List<Modem> requests = new ArrayList<>();
		for (int i = 0; i < totalCount; i++) {
			requests.add(Modem.builder()
				.modemNo("modemNo-" + i)
				.imei("imei-" + i)
				.buildCompany("buildCompany-" + i)
				.modemStatusCd(Code.builder().code(MODEM_STAUTS_NORMAL.getCode()).build())
				.modemTypeCd(Code.builder().code(MODEM_TYPE_LORA.getCode()).build())
				.build());
		}
		modemRepository.saveAll(requests);
	}
```

