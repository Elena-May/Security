# Tangled Web Book

## Chapter 1
### Introduction
Confused Deputy Problem - 

Security is usually defined as :
A system is secure if it behaves precisely in the manner intended — and does nothing more.

But this is a nearly useless generalisation as:
- There is no way to define desirable behavior for a sufficiently complex computer system. 
- Wishful thinking does not automatically map to formal constraints - intutitive concept dont necessarily translate to mathematical models - e.g. 'I do not want my mail to be read by others'.
- Software behavior is very hard to conclusively analyze - many cases are impossible to solve in practice, often due to the halting problem.

In the end, regardless of the number of elegant, competing models introduced, all attempts to understand and evaluate the security of real-world software using algorithmic foundations seem bound to fail - what do we do?

### Risk Management
risk = probability of an event x maximum loss 
Learn to live with vulnerabilities using this formula
Whilst prioritising of security is important it introduces the fallacy that structured inadequacy is almost as good as adequacy and that underfunded security efforts plus risk management are about as good as properly funded security work.

This is not true for four reasons:
- There is internal escalation of security risks, losses are not capped
- The nonmonetry costs of intrusions are hard to offset with the value contributed by healthy systems - e.g. user confidence
- Existing data is probably not representative of future risks
- Statistical forecasting is not a robust predictor of individual outcomes

### Practical Approaches to Improving Security:
Learning from other peoples mistakes - ongoing design guidance, ensuring developers know what could go wrong and give them tools to carry out otherwise error = prone tasks in the best way possible.
Developing tools to detect and correct problems - audits on casual mistakes so that they are not only realised when discovered by a malicious party
Planning to have everything compromised - major incidents will occur despite our best efforts to prevent them

## Evolution of a Threat
### The User as a Security Flaw
Users do not need a technical understanding of threats to be able to use a computer
This leads them open to letting security threats in 
Software developers may say this is not the fault of engineering at all
But UIs have not yet been successfully designed in a bulletproof way

Whitelists
Since we’ve mentioned blacklists, thought we’ve mention whitelists as well.
Whitelists are a list of safe somethings, whatever is not on the lists is blocked. Administrators often tailor make whitelists, definitely not on size fits all.
One thing I picked up from the tangled web book is that because the user interfaces has developed now to such a high level that you don’t really need any technical knowledge to use a computer, users are also often not able to detect the threats they might face.
Whitelists help to tackle this. Email whitelists help to stop users receiving fishing emails, website whitelists can be used for internal networks for only certain websites to be accessed, and only allowing approved IP addresses to access your domain.
Whitelisting basically a way of removing security from the users responsibility

### Limited seperation of applications on the Web
In personal computers there are very clear boundaries - a file opened in your text editor is very unlikely to be able to steal your mail.
In the browser world this seperation is non existent, isolation between completely unrelates applications is limited and they usually share the same JavaScript.

### Non convergence of visions
Whilst there are some security mechanisms  to allow rudimentary protection for web applications, there is no shared, holistic security model to live by 

Unix - rwx user/group permission model does this
On the browser - same-origin policy has potential to be duch a model, but it only governs  a smaal amount of cross-domain interactions and there are about 7 versions of it.


## The Breakdown of the Client-Server Divide
The Web used the be a proper example of client-server architecture, functional boundaries between client and server responsibilites were quickly eroded
Culprit - javascript
Language that offers the HTTP servers a way to delegate application logic to the browser ("client") side - means that user interfaces are more responsive and means that server-sode CPU and memory requirements can decrease drastically 
In some applications this is taken to extremes, with the serve becoming little more than storage
The shift in how applications work creates problems for security, this means that  most applications sit in a middle ground with no easily discernible and logical speration of duties between cient and server components.




## Part 1 - Anatomy of the Web 
## Chapter 2 - URLs
Uniform Resource Locator (URL) - simple text string which is meant to conslusively address and uniquely identify a single resource on a remote server.

Generic URL syntax is coded primarily in RFC 3986, its practical uses on the Web are outlined in RFCs 1738.

### Structure 
Two types of URL - 
Fully qualified aabsolute URL - one that specifies all information required to access a particular resource, does not depend in any way on where the navigation began.
Relative URL - omits some of the information and must be interpreted in the context of a base URL

### Absolute URL
- Scheme/protocol name - valid scheme for absolute URl, main difference from relative. It is a string that ends with a single colon and indicates the protocol to be used to retrieve the resource.

- Indicator of a hierarchical URL (constant) - RFC 1738 systax rules, every absoolute URL must contain the fixed string "//". However, sometimes the "//" still appears when the URl is not absolute or doesnt appear when it is. Creates unexpected interpretations by browsers.

http:example.com/ - interpreted as an absolute if no qulaified base URl exists, interpreted as a relative if one does.

- Credentials to access the resource (optional) - may contain a surename and perhaps a password that may be required to retireve data form the server, when no credentials are supplied the browser will attempt to fetch the resource anonymously. In the case of HTTP and several other protocols, this means not sending any authentication data; for FTP, it involves logging into a guest account named ftp with a bogus password.

- Server address to retrieve the data from - case-insensitive DNS name sucha as example.com or a raw IPv4 address such as 127.0.0.1

- Port number to connect to (optional) - option section that describes a nonstandard network port to connect to on the previously specified server.

- Hierarchical Unix path to a resource - a away to identify a specific resource to be retrieved from the server.

- "Query string" parameters (optional) - common example is in the case of a search bar - passing user supplied terms to a server-side script that implements the search functionality.
http://example.com/ **search.php?query=Hello+world**

- "Fragment identifier" (optional) - The fragment ID is an opaque value with a role similar to the query string but that provides optional instructions for the client application rather than the server.


STEP 1: Extract the scheme name.
Scan for the first “:” character. The part of the URL to its left is the scheme name. Bail out if the scheme name does not conform to the expected set of characters; the URL may need to be treated as a relative one if so.

STEP 2: Consume the hierarchical URL identifier.
The string “//” should follow the scheme name. Skip it if found; bail out if not.

STEP 3: Grab the authority section.
Scan for the next “/”, “?”, or “#”, whichever comes first, to extract the authority section from the URL. As mentioned above, most browsers will also accept “ \” as a delimiter in place of a forward slash, which may need to be accounted for. The semicolon (;) is another delimiter in browsers other than Internet Explorer and Safari.

STEP 3A: Find the credentials, if any.
Once the authority section is extracted, locate the (@) in the substring. If found, the leading snippet constitutes login credentials, if a : is present, this can be split into login and password data.

STEP 3B: Extract the destination address.
The remainder of the authority section is the destination address. Look for the first colon to separate the hostname from the port number. A special case is needed for bracket-enclosed IPv6 addresses, too.

STEP 4: Identify the path (if present).
If the authority section is followed immediately by a forward slash—or a backslash or semicolon,— scan for the next “?”, “#”, or end-of-string, whichever comes first. The text in between constitutes the path section, which should be normalized according to Unix path semantics.

STEP 5: Extract the query string (if present).
If the last successfully parsed segment is followed by a question mark, scan for the next “#” character or end-of-string, whichever comes first. The text in between is the query string.

STEP 6: Extract the fragment identifier (if present).
If the last successfully parsed segment is followed by “#”, everything from that character to the end-of-string is the fragment identifier. 

URL passing algorithm relies on the fact that certain characters will not apppear in the URl unless in there capacity of identifying sections of the URL:

: / ? # [] @

The below are also off limits:

! $ & ' ( ) * + , ; =


## Chapter 3 Hypertext Transfer Protocol(HTTP)
Core transfer mechanism of the web 
Preferred method for exhanging URL-referenced documents between servers and clients

Resides on the Application Layer, Layer 7 of the OSI model

TCP/IP based protocol - text, images, audio can all be transmitted through it

HTTP works on a client response cycle, client requests a web page
Stateless Protocol - server does not have to retain information or status about each user for the duration of multiple requests.

HTTP Cookies

Server Side

Sessions 

Variables 

URL Rewriting  

A request is sent to the server known as a HTTP message
The server will prepare a response and send it back 

A HTTP message is made up of three blocks known as:
The start-line,  headers and body 

#### Request message

- Startline is made up of: 
Method, command given to the command line so the command line knows what to do: e.g. GET
URI (unifor resource identifier, the way a server can locate a resource): e.g. /pages/index.html
HTTP version: e.g. HTTP/1.0

- Header: Named Value pairs
HOST (address of server we are sending the request to): www.google.co.uk
Accept (file type being requested): text/html
Accept-langage: en-us

#### Response Message
- Start-line:
http/version 
status code - will tell the client if the request succeeded, these codes include 200 okay, 404 (not found)
- Header
Same information as the request
- Body 
File that was requested

The main issue with HTTP is that it is unencrypted mean that it is unsecure at transferring data between your computer and a server.
When a connection is occurring, anyone can put themself in the middle and snag emails e.t.c.

Why its important to use HTTPS - 
S - stands for Secure
Designed to increase privacy on the internet
HTTP no longer sent as plain text

To secure website - need an SSL certificate, an online identification card, also encrypt any dara that is passed through the HTTPS protocol.

Encrypted and ecrypted between the client an server, if a man in the middle attack takes place, the data is encrypted an unreadable.

When a request is sent out and a response is recieved, no checks are made that the response file is from the server address requested. Could send attacker controlled files and it would not be detected.

In fact, the class of vulnerabilities related to HTTP header newline smuggling—be it due to this inconsistency or just due to a failure to filter any type of a newline—is common enough to have its own name: **header injection** or **response splitting**.

### Resolution of duplicate or conflicting headers 
because of a lack of specification-level advice, roughly half of all browsers will favor the first occurrence of a particular HTTP header, and the rest will favor the last one, ensuring that almost every header injec- tion vulnerability, no matter how constrained, is exploitable for at least some percentage of targeted users.

### HTTP RAT (Remote Access Trojans):
A remote access Trojan - malware program that includes a back door for adminstrative control over the target computer. Usually downloaded invisibly with a user-requested program such as a game or sent as an email attachment.

**Backdoor** - method of bypassing normal authentication or encryption in a computer system.

**Distributed DoS (Denial of Service) attack** -  Makes a machine or network resource unavailable to its intended users. When multiple systems flood the bandwidth or resources of a targeted system, ususally one or more web servers. Often a resut of multiple compromised systems, a botnet, flooding the targeted system with traffic. A botnet is a network of zombie computers programmed to receive commands without the owners' knowledge.

## Chapter 4 Hyper Text Mark Up Language 
Document parsing - top-level <! DOCTYPE>
may be used to instruct the browser to pass the file in a manner that at least superficially conforms to one of the officially defined standards.

XML is more strict, less forgiving than normal HTML

HTML was supposed to have a different tag for every different type of text - link, citation, code snippet
Partly for computational reasoning of human knowledge sometime in the future
However, the language was quickly distilled by developers into a handful of tags which do little to explain the structure of the document to the browser.
The semantics of HTMl matter less and less as JavaScript become the bigger player on interfaces.

## Chapter 5 Cascading Style Sheets 
- @-prefixed directives are recognized in stand-alone stylesheets. All of them modify the meaning of the stylesheet; for example, by specifying the namespace or the display media that the stylesheet should be applied to. 
- @charset, effects parsing, sets  the charset of the current CSS block
- @import, also effects parsing, inserts an external file into the stylesheet.

HTML parsing is performed first and is completely independent of CSS syntax rules. Therefore, it is unsafe to place certain HTML syntax characters inside CSS properties

CSS parsing behaviour, compliant parsers are expected to continue after encountering a syntax error and restart at the next mathcing curly bracket, this creates an oppurtunity to exploit parser incompatibilities  - 
If there is any way to derail a particular CSS implementation with inputs that seem valid to other parsers, the resynchronization logic may cause the attacked browser to resume parsing at an incorrect location, such as in the middle of an attacker-supplied string.

Character encoding:
Place problematic characters such as hexadecimals inside strings by placing a backslash infront of them.
It is also possible to place a backslash infront of a charcter that isn't a hexadecimal digit.
You are also able to use this syntax outside of strings but not inside url values.
These and similar quirks make the detection of known dangerous CSS syntax extremely error prone.


## Chapter 6 JavaScript
#### Script Processing Model 
Every HTML document in a browser is given a seperate instance of the JavaScript execution environment, complete with an individual namespace for all global variables and functions created by the loaded scripts.
Within a particular execution context, all encountered JavaScript blocks are processed individually and almost always in a well-defined order. Each code block must consist of any number of self-contained, well-formed syntax units and will be processed in three distinct, consequent steps: parsing, func- tion resolution, and code execution.

##### Parsing
Parsing stage validates the syntax of the block, converts it to a intermediate binary representation.
Code has no global effects until completetion of this step, if there are syntax errors then the entire block is abandoned.

##### Function Resolution
After successful parsing, the next step involves registering every named, global function that the parser found with the currently processed block.

##### Code Execution 
Once function resolution is completed, the JavaScript engine starts with the ordered execution of all statements outside of function blocks.
The execution of the code may fail at this point sue to an unhandled exception.

#### Execution Ordering Control 

JavaScript engine'e execution ordering and timing model:
While the execution engine is busy, the processing of events, timers, page navigation requests, and so on, is postponed; in most cases, the entire browser, or at least the HTML renderer, will also remain largely unresponsive. Only once the execution stops and the scripting engine enters an idle state will the processing of queued events resume. At this point, the JavaScript code may be entered again.
Further, JavaScript offers no sleep(...) or pause(...) function to temporarily release the CPU and later resume execution from the same location. 
Because of the potentially disruptive, browser-blocking behaviour of JavaScript loops when being implemented, browwsers have the option to mitgate this.
One of the consequences of this is that a endless loop can be terminated in a way similar to throwing an unhandled exception
The engine will then return to the idle state but will remain operational, the offending code will remain callable, and all timers and event handlers will stay in place.
This ability to inexpectedly terminate code can be triggered on purpose by an attacker, causing issues and the termination of code may put the appplication in an inconisitent state by aboritng an operation that the author expects to complete successfully.

#### Modifying the runtime environment 
Executed javascript scripts can have impacts on other documents, creating more possibilities for exploiters.

##### Overriding Built-ins 
Rogue scripts have the ability to delete overwrite or shadow most of the built-in JavaScript functions and virutall all browser-supplied input/output methods.

#### Setters and getters
Wasy to supply custom code for handling the reading and setting of the properties of an object.
Can be used to make existing objects behave in confusing ways.

### JSON and Security 
JSON = Javascript Object Notation 
When exchanging data between a browser and a server, the data can only be text.

JSON is text, and we can convert any JavaScript object into JSON, and send JSON to the server.

We can also convert any JSON received from the server into JavaScript objects.

This way we can work with the data as JavaScript objects, with no complicated parsing and translations.

the risk with json is to decode it using eval() - very easy to do, and dangerous if the json comes from an untrusted source

Instead of eval(), use a JavaScript JSON library. Or you can use the recent JSON.parse() API if you only need to support modern browsers.


## Chapter 7 Non-HTML Document Types

### Plaintext files
nothing about them is formatted, presente as they are found.
All browsers recognise plaintext files served with Content-Type: text/plain in the HTTP headers.
No specific security consequences
Howver, all non-HTMl are at risk of being idnetified by the browser as HTML, attacker controlled plaintext documents are of special concern.

### Bitmap Images
As with text files, bitmap images are a passive resource and carry no unusual security risks.† However, whenever serving user-supplied images, remember that attackers will have a degree of control over the data, even if the format is carefully validated and scaled or recompressed. Therefore, the concerns about such a document format being misinterpreted by a browser or a plug-in still remain.

### Audio and Video 
Ongoing work in HTML5 to support <audio> and <visual> tags.
Content misidenficiation is a security issue again.

### SVG
Scalable Vector Graphics, quickly evolving, XML-based vector graphics format.
Noteworthy for its integrated animation capabilities and direct JavaScript scripting features.
While it is safe to load SVG images via <img> (scripting should be disabled in this scenario), it is fairly dangerous to host user-supplied SVG data because in cases of direct navigation, all embedded scripts will execute in the context of the hosting domain. This unexpected problem means that serving any externally originating SVG images will require very careful syntax sanitization to eliminate non-SVG xmlns content from the XML container and to permit only certain types of markup in the remainder of the document.

## Chapter 8 CONTENT RENDERING WITH BROWSER PLUG-INS
A plugin is a piece of software that acts as an add-on to a web browser and gives the browser additional functionality. Plugins can allow a web browser to display additional content it was not originally designed to display.

In the second case, the browser simply hands over the retrieved file, provides the helper application with a rectangular drawing surface in the document window, and essentially retreats.

Have alot of security flaws historically - according to some analysts, 12 out of 15 most frequently exploited client-side vulnerabilities in 2010 could be attributed to plug-ins.

Many of these problems are because they have been overlooked in the scrutiny over web security,and the underlying parsers still used cannot handle attacks.

#### Invoking a plug-in 

Content-rendering plug-ins can be activated in a couple of ways. The most popular explicit method is to use <embed src=...> or <object data=...> markup in a “host” HTML document, with the src or data parameter pointing to the URL from which the actual plug-in-recognized document is to be retrieved.




# Part 2 - Browser Security Features
All the security features that keep rogue web applications at bay.

## Chapter 9 - Content Isolation Logic
Most security assurances are meant to isolate documents based on their origin.
Two pages from different sources should not be allowed to interfere with each other.
Actual practice can be more complicated - no universal agreement exists about where a single document begins and ends or what constitutes a single origin.

Also little clarity about what actions should need a security check and which shouldn't.

### JavaScript Isolation Logic 
### Same-Origin Policy 
The basic rule behind this policy is straightforward: Given any two separate JavaScript execution contexts, one should be able to access the DOM of the other only if the protocols, DNS names,* and port numbers associated with their host documents match exactly.

SOP can soemtimes be too inflexible for Web Developers.

The policy is sometimes too broad, making it impossible to isolate home pages belonging to seperate users. Narrowing down the concept of an origin has not been successful so far.

In other cases it is not broad enough, the policy makes it difficult for legitmatley co operating sites to seamlessly exchange data.

Attempts to broaden orgins and facilitate cross-domain interactions are mroe common - 

#### document.domain 
This JavaScript property permits any two cooperating websites that share a common top-level domain (e.g. example.com) to agree that for the purpose of future same-origin checks they want to be considered equivalent.
Protocols and port numbers still have to match.
The problem is, that once a connection has been made between two website, any other website with the same domain name attached will allso be able to communicate.
Touching document.domain inevitably entails tying the security of your page to the security of the weakest link in the entire domain.

#### postMessage(...)
Very robust mechanism, not many security flaws, should be used as often as possible.

The mechanism permits a text message of any length to be sent to any window for which the sender holds a valid JavaScript handle.

It allows the sender to specify what origins are permitted to receive the message in the first place (in case the URL of the target window has changed), and it provides the recipient with the identity of the sender so that the integ- rity of the channel can be ascertained easily.

### Interactions with browser controls
Any two windows or frames opened in a browser will remain same-origin with each other even if the user logs out from one account and logs into another.
This means them very exploitable.
everal sites do not protect their login forms against cross-site request forgery, permitting any third-party site to simply submit a username and a password and log the user into an attacker-controlled account. This may seem harmless at first, but when the content loaded in the browser before and after this operation is considered same-origin, the impact of normally ignored “self-inflicted” cross-site scripting vulnerabilities (i.e., ones where the owner of a particular account can target only himself) is suddenly much greater.
The attacker may first open and keep a frame pointing to a sensitive page on the targeted site, then log the attacker into an attacker controlled account on the unrelated site to execute self-xss (victim of the attack unwillingly runs malicious code in their own web browser)


### Chapter 10 - Origin Inheritance 