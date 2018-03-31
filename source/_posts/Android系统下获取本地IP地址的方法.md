title: Android系统下获取本地IP地址的方法
author: superman
date: 2018-02-07 22:26:41
categories:
- android
tags:
- API
---

```
/**
 * 获取本地IP地址
 *
 * @return
 */
public static String getLocalIpAddress() {
	try {
		String ipv4;
		List<NetworkInterface> nilist = Collections.list(NetworkInterface.getNetworkInterfaces());
		for (NetworkInterface ni : nilist) {
			List<InetAddress> ialist = Collections.list(ni.getInetAddresses());
			for (InetAddress address : ialist) {
				if (!address.isLoopbackAddress() &&
						InetAddressUtils.isIPv4Address(ipv4 = address.getHostAddress())) {
					return ipv4;
				}
			}
		}
	} catch (SocketException ex) {
		ex.printStackTrace();
	}
	return null;
}
```