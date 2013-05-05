SuperVariable
=============

I simple wrapper `POST`, `GET` , `REQUEST` or any `Array` in PHP

#### Config 

```PHP
include 'src/Varriable.class.php';
include 'src/filter/Parsable.class.php'; // Interface to allow you extend filter
include 'src/filter/Basic.class.php'; // Basic Filter you can create yours

use \super\filter\Basic;
use \super\Varriable;


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


#### Example 1

```PHP
//Start super Variable
$_POST = new Varriable($_POST);


/**
 * You can Ignore some fields that should not be filtred
 * eg Binary , XML , JSON etc;
 */
$_POST->ignore("image", "binary");

echo $_POST['hello'], PHP_EOL; // array
echo $_POST->hello, PHP_EOL; // object
echo $_POST("hello"), PHP_EOL; // function

echo $_POST->offsetGet("hello"), PHP_EOL; // direct
echo $_POST->hello(), PHP_EOL; // methods
                               
// Lest have more fun and file array based on path
echo $_POST->find("testing.example.filter"), PHP_EOL;

// This ould not be modified because of ignore
echo $_POST['binary'], PHP_EOL;

/*
 * This would return error because modification is disable but it can be enabled
 * $_POST = new Varriable($_POST,null,Varriable::ALLOW_GET |
 * Varriable::ALLOW_SET);
 */
echo $_POST['hello'] = "Modify";
```

#### Output 

It would all give you the same value 

	Hello word
	Hello word
	Hello word
	Hello word
	Hello word
	Let's meet 4:30am �t the caf�	
	Fatal error: Uncaught exception 'ErrorException' with message 'Offset assignment disabled'
	
	
#### Example 2

This class automatically handles Invalid Offset error


```PHP
// Before
$var = isset($_POST['var']) ? $_POST['var'] : null;

// now
$var = $_POST['var']; // No need to check
```


#### Example 3

Supports Filters 

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



####  Example 4

You can limit the filter to specific offset


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
	
	
####  Example 5

Loops

```PHP
// Loop normally
foreach ( $_POST as $v ) {
	print_r($v);
}

// or Recursively
foreach (new RecursiveIteratorIterator($_POST->getRecursiveIterator()) as $k => $v ) {
	echo $v, PHP_EOL;
}
```



####  Example 6

Enable SET and Disable GET

```PHP
$_POST = new Varriable($_POST, null, Varriable::ALLOW_SET);
$_POST['hello'] = "World";

echo $_POST['hello']; // will return error;
                       
// you can only loop or convert the iterator to array
foreach ( $_POST as $v ) {
	print_r($v);
}
```



####  Example 7

Another simple type of filter is Callback 

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
	
	
####  Example 8
You can easly find or inject elements at any position

```PHP

$_POST = new Varriable($_POST, new Basic(Basic::FILTER_XSS));

//Find any elements
echo $_POST->find("object.data.bad"), PHP_EOL; 

//Inject any elements
$_POST->inject("object.data.*", "<span onClick=\"javascript.alert('XSS Test')\" >Test</span>"); // add test to data
$_POST->inject("object.data.range",range(1,3)); //add range to data


print_r($_POST['object']);
```
Output 

	&lt;b&gt;Bad&lt;/b&gt  // echo
	
	stdClass Object // print_r object
	(
	    [name] => delete
	    [data] => stdClass Object
	        (
	            [age] => 21
	            [bad] => &lt;b&gt;Bad&lt;/b&gt;
	            [var_1] => &lt;span onClick=&quot;javascript.alert(&#039;XSS Test&#039;)&quot; &gt;Test&lt;/span&gt;
	            [range] => Array
	                (
	                    [0] => 1
	                    [1] => 2
	                    [2] => 3
	                )
	
	        )
	
	)

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
	 




	
	
