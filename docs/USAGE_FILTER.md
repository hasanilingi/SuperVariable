[ReadME](README.md] | [General Usage](docs/USAGE_GENERAL.md) | [Using Filters](docs/USAGE_FILTER.md)


Using Filters Usage
=============

- [Basic](#BASIC)
- [Filter Specific Key](#SPECIFIC)
- [Filter Ignore](#IGNORE)
- [Callback](#FIND)


#### Fake Data 

```PHP
// Generate Fake post Data

$object = new stdClass();
$object->name = "delete";
$object->data = (object) array("age"=>21,"bad"=>"<b>Bad</b>");

$_POST = array();
$_POST['testing']["name"] = "<b>" . $_SERVER['SERVER_NAME'] . "</b>";
$_POST['testing']["example"]['xss'] = '<IMG SRC=javascript:alert("XSS")>';
$_POST['testing']["example"]['sql'] = "x' AND email IS NULL; --";
$_POST['testing']["example"]['filter'] = "Let's meet  4:30am �t the \tcaf�\n";
$_POST['selected'] = "���������������Hello World�����������������";
$_POST['phone'] = "Phone+888(008)9903";
$_POST['hello'] = "Hello word";
$_POST['image'] = file_get_contents("http://i.imgur.com/YRz0AI7.png");
$_POST['binary'] = mcrypt_create_iv(10, MCRYPT_DEV_URANDOM);
$_POST['object'] = $object ;

```


	
<h2 id="BASIC">Basic Usage</h2>
You call easily filter out XSS Injection
```PHP
$_POST = new Varriable($_POST, new Basic(Basic::FILTER_XSS));
print_r($_POST['example']);
```

Output

	Array
	(
	    [0] => &lt;b&gt;localhost&lt;/b&gt;
	    [1] => Array
	        (
	            [0] => &lt;IMG SRC=javascript:alert(&quot;XSS&quot;)&gt;
	            [1] => x&#039; AND email IS NULL; --
	            [2] => 
	        )
	
	)



<h2 id="SPECIFIC">Filter Specific Key</h2>
You can restrict modification to your varriables.

```PHP
//Before 
echo $_POST['phone'];

//Convert post to super
$_POST = new Varriable($_POST);
$_POST->offsetFilter("phone", new Basic(Basic::FILTER_INT));

//After 
echo $_POST->phone;

```

Output 

	Phone+888(008)9903  //before
	+8880089903         //after 

	
<h2 id="IGNORE">Filter IGNORE</h2>

During filter process Ignore Binray , Hash (md5 , sha ) during Filter

```PHP
$_POST = new Varriable($_POST, new Basic(Basic::FILTER_ALL,
		 Basic::IGNORE_BASE64 | Basic::IGNORE_HEX | Basic::IGNORE_BINARY));

```

<h2 id="SPECIFIC">Callback</h2>

You can use Callback which also supports Regex.

```PHP
$callback = new Callback();

// Add callback to keys when found
$callback->add("hello", function ($value, $key) {
    return strtoupper($value);
});

// You can also use regex with match
$callback->match("/^hello/", function ($value, $key) {
    return strtoupper($value);
});

$_POST = new Varriable($_POST, $callback);
echo $_POST['hello'];

```

Output 

	HELLO
	
	
	
#### Licence [MIT](http://opensource.org/licenses/MIT)

	Copyright (c) 2013 Oleku Konko
	
	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:
	
	The above copyright notice and this permission notice shall be included in
	all copies or substantial portions of the Software.
	
	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
	THE SOFTWARE.
	 



[ReadME](README.md] | [General Usage](docs/USAGE_GENERAL.md) | [Using Filters](docs/USAGE_FILTER.md)
	
	