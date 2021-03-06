# CarsiLog
实现Carsi日志的采集与分析
# Carsi访问日志  
* 日志目录：安装目录中的logs子目录  
* 日志文件：只分析其中的访问日志，当前的日志文件名为idp-audit.log，历史日志文件为压缩后的.gz文件  
* 日志格式：

` ` `
2020-09-02T08:08:22.559+08:00|urn:oasis:names:tc:SAML:2.0:bindings:HTTPRedirect|_d210b1938aced89c897579b2372cf53c|https://fsso.yyy.net/shibboleth|http://shibboleth.net/ns/profiles/saml2/sso/browser|https://idp.sdu.edu.cn/idp/shibboleth|urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST|_90e9fe99257861babd572a4bd624f544|202232122236|urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport|eduPersonScopedAffiliation|AAdzZWNyZXQxg882w41+cgvcRKiDWYnT5e2Ij+AgWlRYGGBXmwgav5MccO8Qw4Y8pMIqGNh9IwhDCSpxzLxU+s6c6vykVhpdyejME1y+JW5hPPVs2W6UbNZcYVSI+4XDNuDVxKjx70QGJwJLCEBbhI=|_7c64c0bb9b25ed4ae399c0b7a7e4e10|119.100.61.1|1a00180e914cbf1e59a73fff4c7af59b707de918f804de436c8158c6fc1ea932|
` ` `
 
对其中的4个字段进行采集并分析：时间、资源、用户ID、源IP
# 分析平台
基于已有的ELK平台实现Carsi日志的分析  
* 当前日志采集使用logstash.conf文件
* 历史日志采集使用logstash-hist.conf文件
# 分析结果
基于ELK平台中的Kibana对日志进行分析，主要包括Carsi运行数据统计、状态监测及可能安全事件的分析。
* 运行数据统计：主要指标包括总访问量、总独立用户数、用户访问统计、资源访问统计、源IP统计等。
* 运行状态监测：主要是对访问量的时序数据的监测
* 安全事件分析：主要是用户访问异常监测，避免资源滥用  
  用户访问数异常：每天内用户访问数异常（>15次/天，>105次/周）  
  单IP用户数异常：每天单IP上的用户访问数异常（>5/天），排除NAT或代理情况，说明该IP上有多个用户ID访问，存在该IP盗用用户ID的可能  
  单用户IP数异常：每个用户访问时源IP的个数（>5/天），排除个人移动情况，说明该用户ID在不同位置使用，存在该用户ID泄露被不同人使用的可能。  
# 组合到一起
将上述分析结果组合成监测大屏，可以定时刷新，实现Carsi运行状态的实时监测。  
![分析结果](https://github.com/qindouya/CarsiLog/blob/master/image024.jpg)
