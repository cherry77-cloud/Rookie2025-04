<div align="center">
  <h1>HTTP中常见的状态码有那些</h1>
</div>

`1XX` 提示信息，协议处理的中间状态
- 表示请求已被接收，需要继续处理。

`2XX` 请求成功
- **`200 成功`**：服务器已成功处理了请求。通常，这表示服务器提供了请求的网页。
- **`201 已创建`**：请求成功并且服务器创建了新的资源。
- **`202 已接受`**：服务器已接受请求，但尚未处理。
- **`203 非授权信息`**：服务器已成功处理了请求，但返回的信息可能来自另一来源。
- **`204 无内容`**：服务器成功处理了请求，但没有返回任何内容。
- **`206 部分内容`**：服务器成功处理了部分`GET`请求，服务器返回的`body`数据是资源的一部分。

`3XX` 请求重定向
- **`301 永久重定向`**：请求的网页已永久移动到新位置。
- **`302 临时重定向`**：说明请求的资源还在，但暂时需要用到另一个`URL`来访问。
- **`304 未修改`**：自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容，重定向到已存在的缓存文件。

`4XX` 请求错误
- **`401 未授权`**：请求要求身份验证，对于需要登录的网页，服务器可能返回此响应。
- **`403 禁止`**：服务器拒绝请求。
- **`404 未找到`**：服务器找不到请求的网页。

`5XX` 服务器错误
- **`500 服务器内部错误`**：服务器遇到错误，无法完成请求。
- **`501 尚未实施`**：服务器不具备完成请求的功能。
- **`502 错误网关`**：服务器作为网关或代理，从上游服务器收到无效响应。
- **`503 服务不可用`**：服务器目前无法使用(由于超载或停机维护)。通常，这只是暂时状态。
- **`504 网关超时`**：服务器作为网关或代理，但是没有及时从上游服务器收到请求。

---

<div align="center">
  <h1>什么是强缓存和协商缓存</h1>
</div>

**强缓存和协商缓存是`HTTP`缓存机制的两种类型，它们用于减少服务器的负担和提高网页加载速度**

## 强缓存
客户端在没有向服务器发送请求的情况下，直接从本地缓存中获取资源。

#### `Expires`强缓存
- 设置一个强缓存时间，此时间范围内，从内存中读取缓存并返回。
- 但是因为`Expires`判断强缓存过期的机制是获取本地时间戳，与之前拿到的资源文件中的`Expires`字段的时间做比较来判断是否需要对服务器发起请求。
- 这里有一个巨大的漏洞：“如果我本地时间不准咋办？”所以目前已经被废弃了。

#### `Cache-Control`强缓存
- 目前使用的强缓存是通过`HTTP`响应头中的`Cache-Control`字段实现，通过`max-age`来告诉浏览器在指定时间内可以直接使用缓存数据，无需再次请求。

---

## 协商缓存
当强缓存失效时，浏览器会发送请求到服务器，通过`ETag`或`Last-Modified`等`HTTP`响应头与服务器进行验证，以确定资源是否被修改。如果资源未修改，服务器返回`304 Not Modified`状态码，告知浏览器使用本地缓存；如果资源已修改，则返回新的资源，浏览器更新本地缓存。这种方式需要与服务器通信，但可以确保用户总是获取最新的内容。

#### 1. 基于`Last-Modified`的协商缓存
- **`Last-Modified`** 是资源的最后修改时间，服务器在响应头部中返回。
- 当客户端读取到`Last-Modified`的时候，会在下次的请求标头中携带一个字段: `If-Modified-Since`，而这个请求头中的`If-Modified-Since`就是服务器第一次修改时候给他的时间。
- 服务器比较请求中的 `If-Modified-Since` 值与当前资源的 `Last-Modified` 值，如果比对的结果是没有变化，表示资源未发生变化，返回状态码 `304 Not Modified`。如果比对的结果说资源已经更新了，就会给浏览器正常返回资源，返回`200`状态。

但是这样的协商缓存有两个缺点：
- 因为是更改文件修改时间来判断的，所以在文件内容本身不修改的情况下，依然有可能更新文件修改时间（比如修改文件名再改回来），这样，就有可能文件内容明明没有修改，但是缓存依然失效了。
- 当文件在极短时间内完成修改的时候（比如几百毫秒）。因为文件修改时间记录的最小单位是秒，所以，如果文件在几百毫秒内完成修改的话，文件修改时间不会改变，这样，即使文件内容修改了，依然不会返回新的文件。

#### 2. 基于`ETag`的协商缓存
将原先协商缓存的比较时间戳的形式修改成了比较文件指纹（根据文件内容计算出的唯一哈希值）。
- **`ETag`** 是服务器为资源生成的唯一标识符（文件指纹），可以是根据文件内容计算出的哈希值，服务端将其和资源一起放回给客户端。
- 客户端在请求头部的 `If-None-Match` 字段中携带上次响应的 `ETag` 值。
- 服务器比较请求中的 `If-None-Match` 值与当前资源的 `ETag` 值，如果匹配，表示资源未发生变化，返回状态码 `304 Not Modified`。
- 如果两个文件指纹不吻合，则说明文件被更改，那么将新的文件指纹重新存储到响应头的`ETag`中并返回给客户端。

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
