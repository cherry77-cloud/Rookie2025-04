## 一. `HTTP`请求报文和响应报文是怎样的，有哪些常见的字段

`HTTP`报文分为请求报文和响应报文。

### 请求报文
请求报文主要由请求行，请求头，空行，请求体构成。

#### ● 请求行包括如下字段：
- **方法`Method`**：指定要执行的操作，如`GET`，`POST`，`PUT`，`DELETE`等。
- **资源路径**：请求资源的`URI`(统一资源标识符)。
- **HTTP版本**：使用的`HTTP`协议版本，如`HTTP/1.1`或`HTTP/2.0`。

#### ● 请求头的字段较多，常使用的包含以下几个：
- **`Host`**：请求的服务器的域名。
- **`Accept`**：客户端能够处理的媒体类型。
- **`Accept-Encoding`**：客户端能够解码的内容编码。
- **`Authorization`**：用于认证的凭证信息，比如`token`数据。
- **`Content-Length`**：请求体的长度。
- **`Content-Type`**：请求体的媒体类型。
- **`Cookie`**：存储在客户端的`cookie`数据。
- **`if-None-Match`**：管理资源的`ETag`值，用于缓存控制。
- **`Connection`**：管理连接的选项，如`keep-alive`。

#### ● 空行
空行是请求头部和请求主体之间的空行，用于分隔请求头部和请求主体。

#### ● 请求体
请求体通常用于`POST`和`PUT`请求，包含发送给服务器的数据。

---

### 响应报文
`HTTP`响应报文是服务器向客户端返回的数据格式，用于传达服务器对客户端请求的处理结果以及相关的数据。一个标准的`HTTP`响应报文通常包含状态行，响应头，空行，响应体。

#### ● 状态行
状态行包含`HTTP`版本，状态码和状态消息。例如：`HTTP/1.1 200 OK`。

#### ● 响应头部
响应头部也是以键值对的形式提供的额外信息，类似于请求头部，用于告知客户端有关响应的详细信息。一些常见的响应头部字段包括：
- **`Content-Type`**：指定响应主体的媒体类型。
- **`Content-Length`**：指定响应主体的长度。
- **`Server`**：指定服务器的信息。
- **`Expires`**：响应的过期时间，之后内容被认为是过时的。
- **`ETag`**：响应体的实体标签，用于缓存和条件请求。
- **`Last-Modified`**：资源最后被修改的日期和时间。
- **`Location`**：在重定向时指定新的资源位置。
- **`Set-Cookie`**：在响应中设置`Cookie`。
- **`Access-Control-Allow-Origin`**：跨源资源共享(`CORS`)策略，指示哪些域可以访问资源。

#### ● 空行
空行在响应头和响应体之间，表示响应头的结束。

#### ● 响应体
响应体是服务端实际传输的数据，可以是文本，`HTML`页面，图片，视频等，也可能为空。

---

## 二. `HTTP`有哪些请求方式

- **`GET`**：请求指定的资源。
- **`POST`**：向指定资源提交数据进行处理请求(例如表单提交)。
- **`PUT`**：更新指定资源。
- **`DELETE`**：删除指定资源。
- **`HEAD`**：获取报文首部，不返回报文主体。
- **`OPTIONS`**：查询服务器支持的请求方法。
- **`PATCH`**：对资源进行部分更新。

---

## 三. `GET`请求和`POST`请求的区别

- **用途**：`GET`请求通常用于获取数据，`POST`请求用于提交数据。
- **数据传输**：`GET`请求将参数附加在`URL`之后，`POST`请求将数据放在请求体中。
- **安全性**：`GET`请求由于参数暴露在`URL`中，安全性较低；`POST`请求参数不会暴露在`URL`中，相对更安全。
- **数据大小**：`GET`请求收到`URL`长度限制，数据量有限；`POST`请求理论上没有大小限制。
- **幂等性**：`GET`请求是幂等的，即多次执行相同的GET请求，资源的状态不会改变；`POST`请求不是幂等的，因为每次提交都可能改变资源状态。
- **缓存**：`GET`请求可以被缓存，`POST`请求默认不会被缓存。

---
```go
const (
	StatusContinue           = 100 // RFC 9110, 15.2.1
	StatusSwitchingProtocols = 101 // RFC 9110, 15.2.2
	StatusProcessing         = 102 // RFC 2518, 10.1
	StatusEarlyHints         = 103 // RFC 8297

	StatusOK                   = 200 // RFC 9110, 15.3.1
	StatusCreated              = 201 // RFC 9110, 15.3.2
	StatusAccepted             = 202 // RFC 9110, 15.3.3
	StatusNonAuthoritativeInfo = 203 // RFC 9110, 15.3.4
	StatusNoContent            = 204 // RFC 9110, 15.3.5
	StatusResetContent         = 205 // RFC 9110, 15.3.6
	StatusPartialContent       = 206 // RFC 9110, 15.3.7
	StatusMultiStatus          = 207 // RFC 4918, 11.1
	StatusAlreadyReported      = 208 // RFC 5842, 7.1
	StatusIMUsed               = 226 // RFC 3229, 10.4.1

	StatusMultipleChoices   = 300 // RFC 9110, 15.4.1
	StatusMovedPermanently  = 301 // RFC 9110, 15.4.2
	StatusFound             = 302 // RFC 9110, 15.4.3
	StatusSeeOther          = 303 // RFC 9110, 15.4.4
	StatusNotModified       = 304 // RFC 9110, 15.4.5
	StatusUseProxy          = 305 // RFC 9110, 15.4.6
	_                       = 306 // RFC 9110, 15.4.7 (Unused)
	StatusTemporaryRedirect = 307 // RFC 9110, 15.4.8
	StatusPermanentRedirect = 308 // RFC 9110, 15.4.9

	StatusBadRequest                   = 400 // RFC 9110, 15.5.1
	StatusUnauthorized                 = 401 // RFC 9110, 15.5.2
	StatusPaymentRequired              = 402 // RFC 9110, 15.5.3
	StatusForbidden                    = 403 // RFC 9110, 15.5.4
	StatusNotFound                     = 404 // RFC 9110, 15.5.5
	StatusMethodNotAllowed             = 405 // RFC 9110, 15.5.6
	StatusNotAcceptable                = 406 // RFC 9110, 15.5.7
	StatusProxyAuthRequired            = 407 // RFC 9110, 15.5.8
	StatusRequestTimeout               = 408 // RFC 9110, 15.5.9
	StatusConflict                     = 409 // RFC 9110, 15.5.10
	StatusGone                         = 410 // RFC 9110, 15.5.11
	StatusLengthRequired               = 411 // RFC 9110, 15.5.12
	StatusPreconditionFailed           = 412 // RFC 9110, 15.5.13
	StatusRequestEntityTooLarge        = 413 // RFC 9110, 15.5.14
	StatusRequestURITooLong            = 414 // RFC 9110, 15.5.15
	StatusUnsupportedMediaType         = 415 // RFC 9110, 15.5.16
	StatusRequestedRangeNotSatisfiable = 416 // RFC 9110, 15.5.17
	StatusExpectationFailed            = 417 // RFC 9110, 15.5.18
	StatusTeapot                       = 418 // RFC 9110, 15.5.19 (Unused)
	StatusMisdirectedRequest           = 421 // RFC 9110, 15.5.20
	StatusUnprocessableEntity          = 422 // RFC 9110, 15.5.21
	StatusLocked                       = 423 // RFC 4918, 11.3
	StatusFailedDependency             = 424 // RFC 4918, 11.4
	StatusTooEarly                     = 425 // RFC 8470, 5.2.
	StatusUpgradeRequired              = 426 // RFC 9110, 15.5.22
	StatusPreconditionRequired         = 428 // RFC 6585, 3
	StatusTooManyRequests              = 429 // RFC 6585, 4
	StatusRequestHeaderFieldsTooLarge  = 431 // RFC 6585, 5
	StatusUnavailableForLegalReasons   = 451 // RFC 7725, 3

	StatusInternalServerError           = 500 // RFC 9110, 15.6.1
	StatusNotImplemented                = 501 // RFC 9110, 15.6.2
	StatusBadGateway                    = 502 // RFC 9110, 15.6.3
	StatusServiceUnavailable            = 503 // RFC 9110, 15.6.4
	StatusGatewayTimeout                = 504 // RFC 9110, 15.6.5
	StatusHTTPVersionNotSupported       = 505 // RFC 9110, 15.6.6
	StatusVariantAlsoNegotiates         = 506 // RFC 2295, 8.1
	StatusInsufficientStorage           = 507 // RFC 4918, 11.5
	StatusLoopDetected                  = 508 // RFC 5842, 7.2
	StatusNotExtended                   = 510 // RFC 2774, 7
	StatusNetworkAuthenticationRequired = 511 // RFC 6585, 6
)
```
---
```go
type IRoutes interface {
    Use(...HandlerFunc) IRoutes

    Handle(string, string, ...HandlerFunc) IRoutes
    Any(string, ...HandlerFunc) IRoutes
    GET(string, ...HandlerFunc) IRoutes
    POST(string, ...HandlerFunc) IRoutes
    DELETE(string, ...HandlerFunc) IRoutes
    PATCH(string, ...HandlerFunc) IRoutes
    PUT(string, ...HandlerFunc) IRoutes
    OPTIONS(string, ...HandlerFunc) IRoutes
    HEAD(string, ...HandlerFunc) IRoutes
    Match([]string, string, ...HandlerFunc) IRoutes

    StaticFile(string, string) IRoutes
    StaticFileFS(string, string, http.FileSystem) IRoutes
    Static(string, string) IRoutes
    StaticFS(string, http.FileSystem) IRoutes
}
```
