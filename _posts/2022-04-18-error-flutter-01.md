---
layout: post
title:  "[ERROR] No Podfile found in the project directory. (Mac M1)"
date:   2022-04-04 16:00:00 +0900
categories: flutter
tags: flutter, error
comments: 1
---

flutter를 빌드하는데, CocoaPods를 설치하지 않아서 에러가 났다. 
그래서 설치했다. 

{% highlight bash %}
	sudo gem install cocoapods
{% endhighlight %}


그리고는 pod를 설치해보았다. 

{% highlight bash %}
	cd ios
	pod install
{% endhighlight %}

헉.. 그런데 또 오류 프로젝트 내에서 Podfile이 없다고 한다.
그래서 다음과 같은 처리를 하였다. 

{% highlight bash %}
	sudo arch -x86_64 gem install ffi
	cd ios
	arch -x86_64 pod install
{% endhighlight %}

### 참고 자료
- 
