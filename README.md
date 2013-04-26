SuperVariable
=============

I simple wrapper `POST`, `GET` , `REQUEST` or any `Array` in PHP

#### Config 

```PHP
	
include 'src/Varriable.class.php';
include 'src/filter/Parsable.class.php'; // Interface to allow you extend filter
include 'src/filter/Basic.class.php'; // Basic Filter you can create yours

use \super\filter\Parsable;
use \super\filter\Basic;

use \super\Varriable;


$var = array();
$var["name"] = "<b>" . $_SERVER['SERVER_NAME'] . "</b>";
$var["example"]['xss'] = '<IMG SRC=javascript:alert("XSS")>';
$var["example"]['sql'] = "x' AND email IS NULL; --";
$var["example"]['filter'] = "Let's meet  4:30am �t the \tcaf�\n";


//Set fake post data
$_POST['testing'] = $var;
$_POST['selected'] = "���������������Hello World�����������������";
$_POST['phone'] = "Phone+888(008)9903";
$_POST['hello'] = "Hello word";

```


#### Example 1

```PHP
echo $_POST['hello'], PHP_EOL; // array
echo $_POST->hello, PHP_EOL; // object
echo $_POST("hello"), PHP_EOL; // function

echo $_POST->hello(), PHP_EOL; // methods
echo $_POST->offsetGet("hello"), PHP_EOL; // direct
                                 
// Lest have more fun
echo $_POST->find("testing.example.filter"), PHP_EOL; // access fulll array path with find
```

#### Output 

It would all give you the same value 

	Hello word
	Hello word
	Hello word
	Hello word
	Hello word
	Let's meet 4:30am �t the caf�	
	
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
	
	
####  Example 4

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

#### Licence
##### *** Please Note that this is still exprimental

	Copyright 2012 Oleku Konko
	
	Licensed under the Apache License, Version 2.0 (the "License");
	you may not use this file except in compliance with the License.
	You may obtain a copy of the License at
	
	http://www.apache.org/licenses/LICENSE-2.0
	
	Unless required by applicable law or agreed to in writing, software
	distributed under the License is distributed on an "AS IS" BASIS,
	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	See the License for the specific language governing permissions and
	limitations under the License.
	 




	
	