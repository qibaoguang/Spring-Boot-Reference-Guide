### 附录D.5.1 Zip实体压缩

对于一个嵌套jar的ZipEntry必须使用`ZipEntry.STORED`方法保存。这是需要的，这样我们可以直接查找嵌套jar中的个别内容。嵌套jar的内容本身可以仍旧被压缩，正如外部jar的其他任何实体。
