<html>
<head>
<title>esp8266-pythonista</title>
</head>
<body>

<h1>esp8266 y Pythonista</h1>
<p>proximamente veremos como comunicar la placa esp8266 con pythonista.</p>
<ul>
  {% for post in site.posts %}
    <li>
      <a href="/esp8266-pythonista{{post.url }}">{{post.title}}</a>
      {{post.url }}
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>



</body>
</html>
