---
layout: post
title: Linux libuv socket server example
---
```c
#include <stdio.h>
#include <stdlib.h>
#include <uv.h>

// uv_handle_t > uv_stream_t > uv_tcp_t

const int backlog = 128;
const int buffer_size = 1024;
uv_fs_t open_req;
uv_fs_t read_req;
uv_tcp_t *client; // 연결해온 클라이언트(처리를 편하게 하기 위해)

void on_new_connection(uv_stream_t *server, int status);
uv_buf_t alloc_buffer(uv_handle_t *handle, size_t suggested_size);
void on_client_read(uv_stream_t *client, ssize_t nread, uv_buf_t buf);
void on_client_write(uv_write_t *req, int status);
void on_file_open(uv_fs_t *req);
void on_file_read(uv_fs_t *req);

// 클라이언트와 연결 후, 클라이언트에서 데이터를 로드
void on_new_connection(uv_stream_t *server, int status) {
  if (status == -1) {
    fprintf(stderr, "error on_new_connection");
    uv_close((uv_handle_t*) client, NULL);
    return;
  }

  // 클라이언트를 유지하기위한 메모리를 확보
  client = (uv_tcp_t*) malloc(sizeof(uv_tcp_t));
  // loop에 등록
  uv_tcp_init(uv_default_loop(), client);

  // accept
  int result = uv_accept(server, (uv_stream_t*) client);

  if (result == 0) { // success
    // 클라이언트에서 데이터를 읽어 내고, alloc_buffer에서 확보 한 영역에 기록 callback을 호출
    uv_read_start((uv_stream_t*) client, alloc_buffer, on_client_read);
  } else { // error
    uv_close((uv_handle_t*) client, NULL);
  }
}

// suggeseted_size에서 전달 된 영역을 확보
uv_buf_t alloc_buffer(uv_handle_t *handle, size_t suggested_size) {
  return uv_buf_init((char*) malloc(suggested_size), suggested_size);
}

// tcp 클라이언트에서받은 파일 이름을 열기
void on_client_read(uv_stream_t *_client, ssize_t nread, uv_buf_t buf) {
  if (nread == -1) {
    fprintf(stderr, "error on_client_read");
    uv_close((uv_handle_t*) client, NULL);
    return;
  }

  // 클라이언트로부터 받은 데이터
  // 데이터는 buf.base에 저장되어있다.
  char *filename = buf.base;
  int mode = 0;

  // 파일 열기
  uv_fs_open(uv_default_loop(), &open_req, filename, O_RDONLY, mode, on_file_open);
}

// client에 돌려 준 뒤 뒷정리
void on_client_write(uv_write_t *req, int status) {
  if (status == -1) {
    fprintf(stderr, "error on_client_write");
    uv_close((uv_handle_t*) client, NULL);
    return;
  }

  // 메모리 해제
  free(req);
  // 여기에 해제하기 위해 붙여 놓았다.
  char *buffer = (char*) req->data;
  free(buffer);

  // tcp connection 닫기
  uv_close((uv_handle_t*) client, NULL);
}

// 열려있는 파일의 내용을 보기
void on_file_open(uv_fs_t *req) {
  if (req->result == -1) {
    fprintf(stderr, "error on_file_read");
    uv_close((uv_handle_t*) client, NULL);
    return;
  }

  // 파일로부터 읽어 들인 데이터를 저장할 버퍼
  char *buffer = (char *) malloc(sizeof(char) * buffer_size);

  // 로드 등록, 쓰기 버퍼를 지정
  int offset = -1;
  // data 필드에 넣어 콜백 함수에 전달
  read_req.data = (void*) buffer;
  // 이곳은 비동기 콜백
  uv_fs_read(uv_default_loop(), &read_req, req->result, buffer, sizeof(char) * buffer_size, offset, on_file_read);
  // read 등록하면 해제
  uv_fs_req_cleanup(req);
}

// 파일의 내용을 클라이언트에 반환
void on_file_read(uv_fs_t *req) {
  if (req->result < 0) {
    fprintf(stderr, "error on_file_read");
    uv_close((uv_handle_t*) client, NULL);
  } else if (req->result == 0) { // 읽은 후 닫기
    uv_fs_t close_req;
    uv_fs_close(uv_default_loop(), &close_req, open_req.result, NULL);
    uv_close((uv_handle_t*) client, NULL);
  } else { // 읽은 내용을 클라이언트에 반환
    // 쓰기 영역 확보
    uv_write_t *write_req = (uv_write_t *) malloc(sizeof(uv_write_t));

    // 클라이언트에 반환 내용
    // req->data 에 들어가있는 버퍼의 포인터로부터 로드
    char *message = (char*) req->data;

    // uv_write 사용 uv_buf_t 준비
    uv_buf_t buf = uv_buf_init(message, sizeof(message));
    buf.len = req->result;
    buf.base = message;
    int buf_count = 1;

    // on_client_write 내에서 해제 할 수 있도록
    // 포인터를 저장하고 전달한다.
    write_req->data = (void*) message;

    // client로 buf를 기록
    uv_write(write_req, (uv_stream_t*) client, &buf, buf_count, on_client_write);
  }
  // 동기화에 해제
  uv_fs_req_cleanup(req);
}

int main(void) {
  // Network I/O 구조체
  uv_tcp_t server;
  // loop에 등록
  uv_tcp_init(uv_default_loop(), &server);
  // 주소 가져오기
  struct sockaddr_in bind_addr = uv_ip4_addr("0.0.0.0", 7000);
  // bind
  uv_tcp_bind(&server, bind_addr);

  // uv_stream_t: uv_handle_t의 서브클래스, uv_tcp_t의 부모
  // listen
  int r = uv_listen((uv_stream_t*) &server, backlog, on_new_connection);
  if (r) {
    // 에러 처리
    fprintf(stderr, "error uv_listen");
    return 1;
  }

  // loop 시작
  uv_run(uv_default_loop());

  return 0;
}
```