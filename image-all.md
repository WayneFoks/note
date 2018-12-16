# 漂泊的图片的一生

移动开发中，没有人能绕过去的问题是图片加载。因此经常遇到图片相关的技术问题，比如图片的加载速度优化，图片异常的定位。图片是非常难得的一个挖掘全栈技术点、实践成本低的技术场景。

我尝试从星星点点的问题中，汇总一下技术知识。

## 图片的一生有多长

| 节点 | 故事 |
| :--- | :--- |
| 生产 |  |
| 云存储 |  |
| 负载平衡 |  |
| 网关 |  |
| 客户端 |  |

## 图片的百态

### JPEG

1. DCT

## 图片的犯罪行径

图片发现异常，可以从上游到下游逐步排查，但是有些节点是可以直接排除的。

<table>
  <thead>
    <tr>
      <th style="text-align:left">节点</th>
      <th style="text-align:left">嫌疑和处理</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">生产</td>
      <td style="text-align:left">原图有问题</td>
    </tr>
    <tr>
      <td style="text-align:left">云存储</td>
      <td style="text-align:left">图床处理问题</td>
    </tr>
    <tr>
      <td style="text-align:left">负载平衡</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">网关</td>
      <td style="text-align:left">
        <p>http：尽量用https</p>
        <p>https：是否被dns劫持，考虑用httpdns</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">客户端</td>
      <td style="text-align:left">
        <p>下载：Content-Length和Content-MD5验证</p>
        <p>各级缓存：有IO的保证文件完整，可以校验MD5，或者用文件拷贝的方式</p>
        <p>解码：-</p>
        <p>前端展现：-</p>
      </td>
    </tr>
  </tbody>
</table>[Oss的下发建议Content-MD5和Content-Length](https://help.aliyun.com/document_detail/31978.html?spm=a2c4g.11186623.2.10.5c3c608dE6Tx6o)

> 根据协议RFC 1864对消息内容（不包括头部）计算Md5值获得128比特位数字，对该数字进行base64编码作为一个消息的Content-Md5值。该请求头可用于消息合法性的检查（消息内容是否与发送时一致）。虽然该请求头是可选项，OSS建议用户使用该请求头进行端到端检查。 
>
> 请求头中的Content-Length值小于实际请求体（Body）中传输的数据长度，OSS仍将成功创建文件；但Object大小只等于Content-Length中定义的大小，其他数据将被丢弃。

