# About
Jath is a template language that lets you declaratively transform XML documents into
Javascript objects using JSON markup and XPath selectors. The idea is to write
some JSON markup that looks like the object that you want to end up with, and then
add XPath selectors to tell Jath where the data should come from.

# Synopsis

**Turn:**

	<statuses userid="djn">
		<status id="1">
			<message>Hello</message>
		</status>
		<status id="3">
			<message>Goodbye</message>
		</status>
	</statuses>

**into:**

	[ 
		{ id: "1", message: "Hello" }, 
		{ id: "3", message: "Goodbye" } 
	]

**using:**

	var xml = <your AJAXy call here>;
	var template = [ "//status", { id: "@id", message: "message" } ];
	var result = Jath.parse( template, xml );

Check out samples.html for more examples.

# Example
Say we were parsing an XML stream of status updates from a service
like Twitter. The data might look something like this:

	<statuses userid="djn">
		<status id="1">
			<message>Hello</message>
		</status>
		<status id="3">
			<message>Goodbye</message>
		</status>
		<status id="5">
		</status>
	</statuses>

We want to consume this data on the client in Javascript. Let's start with
a template that will create our status object. We want to have the id and the
message as attributes of the object, so we start like this:

	var template = { id: "", message: "" };

When Jath processes a template, the field names 'id' and 'message' will be used
to hold the data in the resulting Javascript objects. Now that we have described
the result format, we need to select where the data comes from. This is where
XPath comes in. Relative to the <status> tag, the XPath selector for the id is 

	@id

and the selector for the message is simply

	message

So, let's add that to the template:

	var template = { id: "@id", message: "message" };

We could tell Jath to process this template as-is, but to be really useful to us
we want it to process all of the statuses and return them to us as an array. The
array template form in Jath is an array literal with one small difference: the 
first element of the array is the XPath selector that returns the collection that
the array will hold. The second element is the array item template. 

So to get the statuses in our example we want to use an XPath selector such as

	//status

giving us a template in the form of 

	var template = [ "//status", { ... } ];

We already know what our item template looks like, so let's insert into the array
template form

	var template = [ "//status", { id: "@id", message: "message" } ];

Processing the template looks like this

	Jath.parse( template, xml )

where xml is an XML document as a Firefox XML DOM object. The resulting Javascript
array, expressed in JSON, would look something like the following

	var result = [ { id: "1", message: "Hello" }, { id: "3", message: "Goodbye" }, ... ];

Jath does not support anything other than string data right now, hopefully
that will change soon.

Literal content may be added to the template by prefixing the value with a semicolon. For example:

    var template [ "//status", { id: "@id", message: ":literaldata" } ];

The character used to denote literal data can be changed by assigning a different value to:
    
    JSON.literalChar

Only a single character may be used as a denotator, not a longer string.

# Status
This software is a proof of concept. There are cases that it cannot handle,
and it isn't production-ready. It is not well tested, and the code here is probably
only sufficient to serve as a reference implementation of the idea.

# Supported environments
Jath fully supports Firefox, Safari, Chrome and Windows Script Host. Opera is mostly supported, and IE lacks namespace handling.

# Limitations

- No support for dates or numeric types

# License
Jath is provided under the MIT free software license. See the file LICENSE for 
the full text.
