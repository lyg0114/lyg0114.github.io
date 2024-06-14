---
title: "WebSocket, JdbcTemplate을 활용한 엑셀업로드기능을 구현"
date: 2024-06-13 08:40:00 +09:00
categories: [ project ]
tags: [ websocket, os, network, tcp/ip, osi 7 layer, 네트워크, JdbcTemplate ]
---

진행중인 [사이드 프로젝트](https://github.com/lyg0114/install)에 엑셀 일괄 업로드 기능을 구현하며 정리한 내용이다. 과거에
비스한 기능을 구현한적이 있다. 돌아보니 당시 나의 구현방식에는 문제점이 3가지 정도 있었다.

1. #### 사용자가 업로드 진행상황이 어느정도인지 파악하기 어려움
- 사용자는 엑셀업로드 작업이 진행되고 있어도, 현재 어느정도 작업이 완료되었는지 알길이 없음

2. #### 작업 속도가 너무 느림
- `spring-data-jpa`의 `ListCrudRepository` 인터페이스에서 제공하는 `saveAll()` 메서드를 사용하여 `INSERT` 작업을 진행 하였다. 
- `saveAll()` 메서드는 내부적으로 for loop 를 돌며 단건으로 `INSERT` 쿼리를 DB에 전달하기 때문에 매요청마다 네트워크자원 사용이 발생한다. 
   그래서 이 방식의 문제점은 **'매우 느리다'** 라는 것이다.

3. #### 예외 처리가 부족함
- 사용자가 엑셀파일에 잘못된 내용을 입력했을때 사용자아게 적절하게 알려주기가 힘들었다. 당시에는 문제가 되는 행,열 정보를 모아서 사용자에게 `alert()`로 알려 주었는데
아주 좋지않은 방법인것 같다. 좀더 개선해 보자.

이번에 비슷한 엑셀 업로드 기능을 구현하되 위의 문제점들을 개선하여 구현해 보고자 한다.

## 1. 업로드 진행상황 표시 개선

이 문제를 해결하기 위해서는 작업 진행상황 데이터를 클라이언트측에 전달할 수 있어야 한다. 요청에 대한 반환이 아닌 서버측이 주체가 되어 클라이언트 쪽으로
데이터를 전달해야 한다. 
이것은 기존 `http` 방식으로는 해결하기 어렵다. 그래서 [WebSocket](https://en.wikipedia.org/wiki/WebSocket)을 도입 하였다.
스프링에서도 역시 WebSocket 지원하는 [프로젝트](https://spring.io/guides/gs/messaging-stomp-websocket)가 있다.

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

핸들러 클래스에서 주목할 부분은 두가지 이다.

1. #### ConcurrentHashMap<String, WebSocketSession> sessions
  - 초기 형태는 `ConcurrentHashMap이` 아닌 `WebSocketSession session` 하나만 필드로 가지고 있었다.
    이경우 `ProgressWebSocketHandler` 클래스는 **Bean**으로 등록 되어 있기 때문에 여러 사용자가 동시에 엑셀 업로드를 진행하면 **동시성 문제**가 발생하였다.
    따라서 여러 요청에대해 유연하게 대응 할 수 있도록 `ConcurrentHashMap을` 통해 `sessionID`를 두어 여러개의 `session`을 생성하여 동시성 문제를 해결하였다. 
   
2. #### sendProgressUpdate()
  - 클라이언트 측으로 진행상황 데이터를 전송하는 메서드.

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

- 클라이언트는 서버에 엑셀 파일을 전달함과 동시에 **sessionId**를 요청후 **sessionId**를 기반으로 **WebSocket**연결을 진행한다.
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

이로서 WebSocket을 활용하여 작업 진행 상황을 사용자가 확인할 수 있게 되었다.


## 2. 데이터 일괄 저장 (Bulk Insert)
**spring-data-jpa**를 사용하면 쉽게 insert를 구현할 수 있다. 다만 위에서 언급하였든 가장 큰 문제는 **느리다** 

<img src="/assets/img/bulk_insert_time.jpg" alt="x609" width="600">

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

### SpringDataJpa
- 작성해야할 코드는 훨씬 간결하지만, 성능이 매우 아쉬운 방식이다.
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

## 결론
이로서 앞서 언급했던 1,2의 문제가 해결 되었다. 3번은 여러가지 해결책을 구상하고 있는데 아직 정하지는 않았다. 조만간 구현하여 정리해야 겠다. 

