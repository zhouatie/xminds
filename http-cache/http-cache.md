# 浏览器http缓存

### 浏览器http缓存大致流程图
![浏览器http缓存](./http-cache.png)

### 通用首部字段
#### cache-control

##### 缓存请求指令

<table>
  <thead>
    <tr>
        <th>指令</th>
        <th>参数</th>
        <th>说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>no-cahce</td>
      <td>无</td>
      <td>强制向源服务器再次验证</td>
    </tr>
    <tr>
      <td>no-store</td>
      <td>无</td>
      <td>不缓存请求或响应的任何内容</td>
    </tr>
    <tr>
      <td>max-age = [秒]</td>
      <td>必需</td>
      <td>期望在指定时间内的响应仍有效</td>
    </tr>
    <tr>
      <td>max-state( = [秒])</td>
      <td>可省略</td>
      <td>接收已过期的响应</td>
    </tr>
    <tr>
      <td>min-fresh = [秒]</td>
      <td>必需</td>
      <td>期望在指定时间内发响应仍有效</td>
    </tr>
    <tr>
      <td>no-transform</td>
      <td>无</td>
      <td>代理不可更改媒体类型</td>
    </tr>
    <tr>
      <td>only-if-cached</td>
      <td>无</td>
      <td>从缓存获取资源</td>
    </tr>
    <tr>
      <td>cache-extension</td>
      <td>-</td>
      <td>新指令标记[token]</td>
    </tr>
  </tbody>
</table>

##### 缓存响应指令

<table>
  <thead>
    <tr>
        <th>指令</th>
        <th>参数</th>
        <th>说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>public</td>
      <td>无</td>
      <td>可向任意方提供响应的缓存</td>
    </tr>
    <tr>
      <td>private</td>
      <td>可省略</td>
      <td>仅向特定用户返回响应</td>
    </tr>
    <tr>
      <td>no-cahce</td>
      <td>可省略</td>
      <td>缓存前必需先确认其有效性</td>
    </tr>
    <tr>
      <td>no-store</td>
      <td>无</td>
      <td>不缓存请求或响应的任何内容</td>
    </tr>
    <tr>
      <td>no-transform</td>
      <td>无</td>
      <td>代理不可更改媒体类型</td>
    </tr>
    <tr>
      <td>must-revalidate</td>
      <td>无</td>
      <td>可缓存但必须再想源服务器进行确认</td>
    </tr>
    <tr>
      <td>proxy-revalidate</td>
      <td>无</td>
      <td>要求中间缓存服务器对缓存的响应有效性再进行确认</td>
    </tr>
    <tr>
      <td>max-age = [秒]</td>
      <td>必需</td>
      <td>响应的最大Age值</td>
    </tr>
    <tr>
      <td>s-maxage = [秒]</td>
      <td>必需</td>
      <td>公共缓存服务器响应的最大Age值</td>
    </tr>
    <tr>
      <td>cache-extension</td>
      <td>-</td>
      <td>新指令标记[token]</td>
    </tr>
  </tbody>
</table>

##### 表示是否能缓存的指令

public指令

`Cache-Control: public` 
> 当使用public指令时，则明确表明其他用户也可利用缓存。

private指令

`Cache-Control: private` 
> 当使用private指令时，响应只以特定的用户作为对象，这与public指定的行为相反。
> 缓存服务器会对该特定用户提供资源缓存的服务，对于其他用户发送过来的请求，代理服务器则不会返回缓存

no-cache指令

`Cache-Control: no-cache`
> 使用no-cache指令的目的是为了防止从缓存中返回过期的资源。
> 客户端发送的请求如果包含no-cache指令，则表示客户端将不会缓存过的响应。于是，“中间”的缓存服务器必须把客户端请求转发给源服务器。如果服务器返回的响应中包含no-cache指令，那么缓存服务器不能对资源进行缓存。源服务器以后也将不再对缓存服务器请求中提出的资源有效性进行确认，且禁止其对响应资源进行缓存操作。

no-store指令

`Cache-Control: no-store`
> 当使用no-store指令时，暗示请求（和对应的响应）或响应中包含机密信息。因此，该指令规定缓存不能再本地存储请求或响应的任一部分。

s-maxage指令
`Cache-Control: s-maxage=604800 (单位 ：秒)`
> s-maxage指令的功能和max-age指令的相同，他们的不同点是s-maxage指令适用于供多位用户使用的公共缓存服务器。

max-age指令

`Cache-Control: max-age = 604800 (单位 ：秒)`
> 当客户端发送的请求中包含max-age指令时，如果判定缓存资源的缓存时间数值比指定时间的数值更小，那么客户端就接收缓存的资源。另外，当指定max-age值为0，那么缓存服务器通常需要将请求转发给源服务器。

> 当服务器返回的响应中包含max-age指令时，缓存服务器将不对资源的有效性再作确认，而max-age数值代表资源保存为缓存的最长时间。

> 应用HTTP/1.1版本的缓存服务器遇到同时存在Expires首部字段的情况时，会优先处理max-age指令，而忽略掉Expires首部字段。而HTTP/1.0版本的缓存服务器的情况却相反，max-age指令会被忽略掉。

min-fresh指令

`Cache-Control: min-fresh=60(单位：秒)`
> min-fresh指令要求缓存服务器返回至少还未过指定时间的缓存资源。比如，当指定min-fresh为60秒后，在这60秒以内如果有超过有效期限的资源都无法作为响应返回了。

max-stale指令

`Cache-Control:max-stale=3600(单位：秒）`
> 使用max-stale可指示缓存资源，即使过期也照常接收。

> 如果指令未指定参数值，那么无论经过多久，客户端都会接收响应；如果指令中指定了具体数值，那么即使过期，只要扔处于max-stale指定的时间内，仍旧会被客户端接收。

only-if-cached指令

`Cache-Control: only-if-cached`
> 使用only-if-cached指令表示客户端仅在缓存服务器本地缓存目标资源的情况下才会要求其返回。换言之，该指令要求缓存服务器不重新加载响应，也不会再次确认资源有效性。若发生请求缓存服务器的本地缓存无响应，则返回状态码504 Gateway Timeout。

must-revalidate指令

`Cache-Control: must-revalidate`
> 使用must-revalidate指令，代理会向源服务器再次验证即将返回的响应缓存目前是否仍然有效。

> 若代理无法连通源服务器再次获取有效资源的话，缓存必须给客户端一条504（Gateway Timeout）状态码。

> 另外，使用must-revalidate指令会忽略请求的max-stale指令（即使已经在首部使用了max-stale,也不会再有效果）。

proxy-revalidate指令

`Cache-Control: proxy-revalidate`
> proxy-revalidate指令要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前，必须再次验证缓存的有效性。

no-transform指令

`Cache-Control: no-transform`
> 使用no-transform指令规定无论是在请求还是响应中，缓存都不能改变实体主体的媒体类型。

Cache-Control扩展

cache-extension token

`Cache-Control: private, community="UCI"`

> 通过cache-extension标记（token），可以扩展Cache-Control首部字段内的指令。

> 如上例，Cache-Control首部字段本身没有community这个指令。借助extension tokens 实现了该指令的添加。如果缓存服务器不能理解community这个新指令，就会直接忽略。因此，extension tokens仅对能理解它的缓存服务器来说是有意义的。

##### Date

首部字段Date表明创建HTTP报文的日期和时间。

HTTP/1.1协议使用在RFC1123中规定的日期时间的格式，如下示例。

`Date: Tue, 03 Jul 2012 04:40:59 GMT`

之前的HTTP协议版本中使用在RFC850中定义的格式，如下所示。

`Date: Tue, 03-Jul-12 04:40:59 GMT`

除此之外，还有一种格式。它与C标准库内的asctime()函数的输出格式一致。

`Date: Tue Jul 03 04:40:59 2012`

##### Pragma

Pragma是HTTP/1.1之前版本的历史遗留字段，仅作为与HTTP/1.0的向后兼容而定义。

`Pragma: no-cache`

> 该首部字段属于通用首部字段，但只用在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

> 所有的中间服务器如果都能以HTTP/1.1为基准，那直接采用Cache-Control: no-cache指定缓存的处理方式是最为理想的。但要整体掌握全部中间服务器使用的HTTP协议版本中却是不现实的。因此，发送的请求会同时含有下面两个首部字段。

```javascript
Cache-Control: no-cache
Pragma: no-cache
```

### 请求首部字段

If-Match