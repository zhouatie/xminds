# 浏览器http缓存

## 浏览器http缓存大致流程图
![浏览器http缓存](./http-cache.png)

## 重要字段介绍

### cache-control

#### 缓存请求指令

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

#### 缓存响应指令

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

#### 表示是否能缓存的指令

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