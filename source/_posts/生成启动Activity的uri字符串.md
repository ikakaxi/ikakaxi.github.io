title: 生成启动Activity的uri字符串
author: superman
date: 2018-02-07 22:35:16
categories:
- android
tags:
- activity
- Intent
---

```
Intent intent = new Intent();
intent.setClass(this, ReceivePushMessageActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.putExtra("name", "li");
intent.putExtra("age", 18);
String intentUri = intent.toUri(Intent.URI_INTENT_SCHEME);

//复制到剪贴板
ClipboardManager cmb = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);
cmb.setText(intentUri);
ToastFactory.showTextShortToast(this, "已复制到剪贴板");
```