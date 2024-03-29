#XSSBuster

**XSSBuster.js** is a proactive DOM sanitizer defending against client-side injection attacks, more specifically, DOM-based and reflected cross-site-scripting attacks.

**XSSB** may <i>NOT</i> alter or pollute the global namespace in any way, thus considered safe to integrate with any existing code/libraries.

#The Problem:
It's a given that third-party scripts integration (be it for web analytics, advertisement, social widgets, et al) do extend the attack surface of your website/webapp, and can open a window for novel client-side injection attacks — most notably, DOM-based XSSes. Here are some stories:
* [http://www.troyhunt.com/2015/07/how-i-got-xssd-by-my-ad-network.html](http://www.troyhunt.com/2015/07/how-i-got-xssd-by-my-ad-network.html)
* [https://blogs.dropbox.com/tech/2015/09/csp-the-unexpected-eval/](https://blogs.dropbox.com/tech/2015/09/csp-the-unexpected-eval)
* [http://www.fuzzysecurity.com/tutorials/14.html](http://www.fuzzysecurity.com/tutorials/14.html)
* [http://blog.mindedsecurity.com/2011/04/god-save-omniture-quine.html](http://blog.mindedsecurity.com/2011/04/god-save-omniture-quine.html)

So, in brief, even a single third-party script integration into your DOM can be too much risk. Furthermore, a CSP policy is not always the answer and [by no means a practical silver bullet for XSS attacks](http://blog.innerht.ml/csp-2015); ...breaking integrated libraries? Requiring non-trivial code changes? [Unsecure whitelisted CDNs](https://github.com/cure53/XSSChallengeWiki/wiki/H5SC-Minichallenge-3:-%22Sh*t,-it's-CSP!%22)? Too much restrictions? [legacy browsers support -IE-](http://caniuse.com/csp)? [Potential XSSIs](https://www.kittenpics.org/wp-content/uploads/2015/11/blackhat-eu-15.pdf)? [Need to allow unsafe-eval](https://blogs.dropbox.com/tech/2015/09/csp-the-unexpected-eval)?

#The Solution:
**XSSB** basically audits all the various input sources -which can be tampered directly by an attacker- on the fly, sanitizing them from any potentially-harmful characters in a whitelist-based manner before they are accessed by other intergrated scripts, thus eliminating the risk of any harmful injection attacks that might occure....

So, basically, **XSSB** offers you the freedom to deploy any given third-party scripts into your DOM, while at the same time covering your DOM's back!

#Performance:
**XSSB** only takes [10ms on average](/tests/perf) to do all required checks alongside registring a few necessary event listeners seamlessly.

#Clarifications:
* The only purpose of this DOM sanitizer is to protect the DOM of your webapp from being exposed to exploitable client-side injection flaws that might be introduced by any integrated third-party scripts.

* While this **XSSB** can practically turn any reflected or DOM-based XSS vulnerabilities fairly unexploitable, it cannot -in any feasible way- defend against stored XSS injections as it's the responsibilty of your back-end code/framework to sanitize any data forged to the server-side before processing with it any further. Hence, a non-strict CSP policy here might come in handy as a defense in depth in conjunction with **XSSB**.

* While significantly hindering the exploitation of DOM-based XSS injections, there might still be a narrow window for limited exploitability in some edge-cases (just remember to [protect any critical variables/objects](https://www.owasp.org/images/a/a3/Mario_Heiderich_OWASP_Sweden_Locking_the_throneroom.pdf)).

##What DOM Properties/Listeners are Covered?##
*Note: The one and only rule here, is not to trust any property that can be set, modified, or influenced by any other origins.*
```javascript
window.name
window.onmessage
window.onhashchange
location.pathname
location.search
location.hash
document.title
document.referrer
parent.parent...window // all parent window objects with permissive CORS
frames[].window // all child frames with location.href !== iframe.src (or those from a different origin)
```

#Usage Instructions:
#####For expiremental deployment, skip to "**Expiremental Deployment**".#####
Simply, place the script tag of **XSSBuster.js** right before any other third-party scripts included into your webpage(s), typically in the top of the head tag, as such:
```html
<head>
<title>example</title>
<script type='text/javascript' src='XSSBuster.js'></script>
<script type='text/javascript' src='thirdParty-script.js'></script>
</head>
```

Beware, as [Dr. Mario](https://twitter.com/0x6D6172696F) once said: "**The order of deployment is everything**". Thus, the deployment precedence of this very **XSSB** is critical...meaning that -ideally- this script has to be included right before any other scripts by all means!

#Expiremental Deployment:
Before the actual deployment of **XSSB** into your webapp, you may consider the report-only version "[variations/XSSB-report.js](/variations/XSSB-report.js)" which doesn't make any changes to the DOM, but rather reports any violations to an endpoint of your choice.

#Compatibility:
**XSSB** is compatible with all modern browsers (Firefox, Chrome, IE9+, Edge and Opera) as well as most -if not all- legacy browsers through fallback functionality....

#Known Issues:
* As **XSSB** makes use of this whitelist character class ```[{\w \/\-+=\_$#@!&*|,.?:<>[\]()'";}]``` within its core regular expression, it may only be compitable with languages that rely on basic latin characters (such as English and French). If this can cause you problems (like breaking the search functionality), please consider using the variation "[variations/XSSB-multilingual.js](/variations/XSSB-multilingual.js)" instead — but note that this variation relies solely on a blacklist-based regex, which is not typically as robust as the original whitelist-based one.

#Credits:
A shoutout to all the people who have supported or contributed to this project in any way. In particular, those whose names are listed below for their significant contributions:
* Ahmed Elsobky
