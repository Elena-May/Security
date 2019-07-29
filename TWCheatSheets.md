# Security Engineering Cheat Sheets for Security

### Chapter 2 

When Constructing Brand-New URLs Based on User Input
 If you allow user-supplied data in path, query, or fragment ID: If one of the section delimiters manages to get through without proper escaping, the URL may have a differ- ent effect from what you intended (for example, linking one of the user-visible HTML buttons to the wrong server-side action). It is okay to err on the side of caution: When inserting an attacker-controlled field value, you can simply percent-escape everything but alphanumerics.

 If you allow user-supplied scheme name or authority section: This is a major code injec- tion and phishing risk! Apply the relevant input-validation rules outlined below.

When Designing URL Input Filters
 Relative URLs: Disallow or explicitly rewrite them to absolute references to avoid trouble. Anything else is very likely unsafe.

 Scheme name: Permit only known prefixes, such as http://, https://, or ftp://. Do not use blacklisting instead; it is extremely unsafe.

 Authority section: Hostname should contain only alphanumerics, “-”, and “.” and can only be followed by “/”, “?”, “#”, or end-of-string. Allowing anything else will backfire. If you need to examine the hostname, make sure to make a proper right-hand substring match.
In rare cases, you might need to account for IDNA, IPv6 bracket notation, port num- bers, or HTTP credentials in the URL. If so, you must fully parse the URL, validate all sec- tions and reject anomalous values, and reserialize them into a nonambiguous, canonical, well-escaped representation.

When Decoding Parameters Received Through URLs
 Do not assume that any particular character will be escaped just because the standard says so or because your browser does it. Before echoing back any URL-derived values or put- ting them inside database queries, new URLs, and so on, scrub them carefully for danger- ous characters.

### Chapter 3
When Handling User-Controlled Filenames in Content-Disposition Headers

 If you do not need non-Latin characters: Strip or substitute any characters except for alpha- numerics, “.”, “-”, and “_”. To protect your users against potentially harmful or deceptive filenames, you may also want to confirm that at least the first character is alphanumeric and substitute all but the rightmost period with something else (e.g., an underscore).
Keep in mind that allowing quotes, semicolons, backslashes, and control characters (0x00–0x1F) will introduce vulnerabilities.

 If you need non-Latin names: You must use RFC 2047, RFC 2231, or URL-style percent encoding in a browser-dependent manner. Make sure to filter out control characters (0x00–0x1F) and escape any semicolons, backslashes, and quotes.
When Putting User Data in HTTP Cookies

 Percent-encode everything except for alphanumerics. Better yet, use base64. Stray quote characters, control characters (0x00–0x1F), high-bit characters (0x80–0xFF), commas, semicolons, and backslashes may allow new cookie values to be injected or the meaning and scope of existing cookies to be altered.
When Sending User-Controlled Location Headers

 Consult the cheat sheet in Chapter 2. Parse and normalize the URL, and confirm that the scheme is on a whitelist of permissible values and that you are comfortable redirecting to the specified host.
Make sure that any control and high-bit characters are escaped properly. Use Puny- code for hostnames and percent-encoding for the remainder of the URL.
When Sending User-Controlled Redirect Headers

 Follow the advice provided for Location. Note that semicolons are unsafe in this header and cannot be escaped reliably, but they also happen to have a special meaning in some URLs. Your choice is to reject such URLs altogether or to percent-encode the “;” charac- ter, thereby violating the RFC-mandated syntax rules.
When Constructing Other Types of User-Controlled Requests or Responses

 Examine the syntax and potential side effects of the header in question. In general, be mindful of control and high-bit characters, commas, quotes, backslashes, and semicolons; other characters or strings may be of concern on a case-by-case basis. Escape or substitute these values as appropriate.

 When building a new HTTP client, server, or proxy: Do not create a new implementation unless you absolutely have to. If you can’t help it, read this chapter thoroughly and aim to mimic an existing mainstream implementation closely. If possible, ignore the RFC-provided advice about fault tolerance and bail out if you encounter any syntax ambiguities.

#### Chapter 4 
 Always output consistent, valid, and browser-supported Content-Type and charset informa- tion to prevent the document from being interpreted contrary to your original intent.

 All other HTML parameter values: Always use quotes around attacker-controlled input. Entity-encode “<”, “>”, “&”, and any stray quotes. Remember that some parameters require additional validation. For URLs, see the cheat sheet in Chapter 2.
Never attempt to blacklist known bad values in URLs or any other parameters; doing so will backfire and may lead to script execution flaws.

 Special parsing modes (e.g., <script> and <style> blocks): For values appearing inside quoted strings, replace quote characters, backslash, “<”, “>”, and all nonprintable characters with language-appropriate escape codes. For values appearing outside strings, exercise extreme caution and allow only carefully validated, known, alphanumeric values.
In XHTML mode, remember to wrap the entire script section in a CDATA block. Avoid cases that require multiple levels of encoding, such as building parameters to the JavaScript eval(...) function using attacker-supplied strings. Never place user-controlled data inside HTML comments, !-type or ?-type tags, and other nonessential or unusually parsed blocks.

#### Chapter 5 
When Loading Remote Stylesheets
 You are linking the security of your site to the originating domain of the stylesheet. Even in browsers that do not support JavaScript expressions inside stylesheets, features such as conditional selectors and url(...) references can be used to exfiltrate portions of your site.4
 When in doubt, make a local copy of the data instead.
 On HTTPS sites, require stylesheets to be served over HTTPS as well.
When Putting Attacker-Controlled Values into CSS
 Strings and URLs inside stand-alone blocks. Always use quotes. Backslash-escape all con- trol characters (0x00–0x1F), “\”, “<”, “>”, “{“, “}”, and quotes using numerical codes. It is also preferable to escape high-bit characters. For URLs, consult the cheat sheet in Chap- ter 2 to avoid code injection vulnerabilities.
 Strings in style parameters. Multiple levels of escaping are involved. The process is error prone, so do not attempt it unless absolutely necessary. If it is unavoidable, apply the above CSS escaping rules first and then apply HTML parameter encoding to the resulting string.
 Nonstring attributes. Allow only whitelisted alphanumeric keywords and carefully vali- dated numerical values. Do not attempt to reject known bad patterns instead.
When Filtering User-Supplied CSS
 Remove all content outside of functional rulesets. Do not preserve or generate user- controlled comment blocks, @-directives, and so on.
 Carefully validate selector syntax, permitting only alphanumerics; underscores; white- spaces; and correctly positioned colons, periods, and commas before “{”. Do not permit complex text-matching selectors; they are unsafe.
 Parse and validate every rule in the { ... } block. Permit only whitelisted properties with well-understood consequences and confirm that they take expected, known safe values. Note that strings passed to certain properties may sometimes be interpreted as URLs even in the absence of a url(...) wrapper.
 Encode every parameter value using the rules outlined earlier in this section. Bail out on any syntax abnormalities.
 Keep in mind that unless specifically prevented from doing so, CSS may position user content outside the intended drawing area or redefine the appearance of any part of the UI of your application. The safest way to avoid this problem is to display the untrusted content inside a separate frame.
When Allowing User-Specified Class Values on HTML Markup
 Ensure that user-supplied content can’t reuse class names that are used for any part of the application UI. If a separate frame is not being used, it’s advisable to maintain separate namespace prefixes.

### Chapter 6 
When Loading Remote Scripts
As with CSS, you are linking the security of your site to the originating domain of the script. When in doubt, make a local copy of the data instead. On HTTPS sites, require all scripts to be served over HTTPS.
When Parsing JSON Received from the Server
Rely on JSON.parse(...) where supported. Do not use eval(...) or the eval-based implementation provided in RFC 4627. Both are unsafe, especially when processing data from third parties. A later implementation from the author of RFC 4627, json2.js,9 is probably okay.
When Putting User-Supplied Data Inside JavaScript Blocks
 Stand-alone strings in <script> blocks: Backslash-escape all control characters (0x00–0x1F), “\”, “<”, “>”, and quotes using numerical codes. It is also preferable to escape high-bit characters.
Do not rely on user-supplied strings to construct dynamic HTML. Always use safe DOM features such as innerText or createTextNode(...) instead. Do not use user-supplied strings to construct second-order scripts; avoid eval(...), setTimeout(...), and so on.
 Stand-alone strings in separately served scripts: Follow the same rules as for <script> blocks. If your scripts contain any sensitive, user-specific information, be sure to account for cross-site script inclusion risks; use reliable parser-busting prefixes, such as “)}]'\n”, near the beginning of a file or, at the very minimum, use a proper JSON serialization with no padding or other tweaks. Additionally, consult Chapter 13 for tips on how to prevent cross-site scripting in non-HTML content.
 Strings in inlined event handlers, javascript: URLs, and so on: Multiple levels of escaping are involved. Do not attempt this because it is error prone. If unavoidable, apply the above JS escaping rules first and then apply HTML or URL parameter encoding, as applicable, to the resulting string. Never use in conjunction with eval(...), setTimeout(...), innerHTML, and such.
 Nonstring content: Allow only whitelisted alphanumeric keywords and carefully validated numerical values. Do not attempt to reject known bad patterns instead.
When Interacting with Browser Objects on the Client Side
 Generating HTML content on the client side: Do not resort to innerHTML, document.write(...), and similar tools because they are prone to introducing cross-site scripting flaws, often in unexpected ways. Use safe methods such as createElement(...) and appendChild(...) and properties such as innerText or textContent to construct the document instead.
 Relying on user-controlled data: Make no assumptions about the escaping rules applied to any values read back from the browser and, in particular, to location properties and other external sources of URLs, which are inconsistent and vary from one implementa- tion to another. Always do your own escaping.

### Chapter 7
When Hosting XML-Based Document Formats
Assume that the payload may be interpreted as XHTML or some other script-enabled docu- ment type, regardless of the Content-Type and the top-level xmlns directive. Do not allow uncon- strained attacker-controlled markup anywhere inside the file. Use the Content-Disposition: attachment if data is not meant to be viewed directly; <img> and feeds will still work.
On All Non-HTML Document Types
Use correct, browser-recognized Content-Type and charset values. Specify the Content-Disposition: attachment where possible. Verify and constrain output syntax. Consult the cheat sheet in Chapter 13 to avoid security problems related to content-sniffing flaws.

### Chapter 8 
When Serving Plug-in-Handled Files
 Data from trusted sources: Data from trusted sources is generally safe to host, but remem- ber that security vulnerabilities in Flash, Java, or Silverlight applets, or in the Adobe Reader JavaScript engine, may impact the security of your domain. Avoid processing user-supplied URLs and generating or modifying user-controlled HTML from within plug-in-executed applets. Exercise caution when using the JavaScript bridge.
 User-controlled simple multimedia: User-controlled multimedia is relatively safe to host, but be sure to validate and constrain the format, use the correct Content-Type, and consult the cheat sheet in Chapter 13 to avoid security problems caused by content-sniffing flaws.
 User-controlled document formats: These are not inherently unsafe, but they have an increased risk of contributing security problems due to plug-in design flaws. Consider host- ing from a dedicated domain when possible. If you need to authenticate the request to an isolated domain, do so with a single-use request token instead of by relying on cookies.
 User-controlled active applications: These are unsafe to host in sensitive domains. When Embedding Plug-in-Handled Files
Always make sure that plug-in content on HTTPS sites is also loaded over HTTPS,* and always explicitly specify the type parameter on <object> or <embed>. Note that because of the non- authoritative handling of type parameters, restraint must be exercised when embedding plug- in content from untrusted sources, especially on highly sensitive sites.
 Simple multimedia: It is generally safe to load simple multimedia from third-party sources, with the caveats outlined above.
 Document formats: These are usually safe, but they carry a greater potential for plug-in and browser content-handling issues than simple multimedia. Exercise caution.
 Flash and Silverlight: In principle, Flash and Silverlight apps can be embedded safely from external sources if the appropriate security flags are present in the markup. If the flags are not specified correctly, you may end up tying the security of your site to that of the provider of the content. Consult the cheat sheet in Chapter 9 for advice.
 Java: Java always ties the security of your service to that of the provider of the content, because DOM access to the embedding page can’t be reliably restricted. See Chapter 9. Do not load Java apps from untrusted sites.
If You Want to Write a New Browser Plug-in or ActiveX Component
Unless you are addressing an important, common-use case that will benefit a significant fraction of the Internet, please reconsider. If you are scratching an important itch, consider doing it in a peer-reviewed, standardized manner as a part of HTML5.

### Chapter 9 
Good Security Policy Hygiene for All Websites
To protect your users, include a top-level crossdomain.xml file with the permitted-cross-domain- policies parameter set to master-only or by-content-type, even if you do not use Flash anywhere on your site. Doing so will prevent unrelated attacker-controlled content from being mis- interpreted as a secondary crossdomain.xml file, effectively undermining the assurances of the same-origin policy in Flash-enabled browsers.
When Relying on HTTP Cookies for Authentication
 Use the httponly flag; design the application so that there is no need for JavaScript to access authentication cookies directly. Sensitive cookies should be scoped as tightly as possible, preferably by not specifying domain at all.
 If the application is meant to be HTTPS only, cookies must be marked as secure, and you must be prepared to handle cookie injection gracefully. (HTTP contexts may overwrite secure cookies, even though they can’t read them.) Cryptographic cookie signing may help protect against unconstrained modification, but it does not defend against replacing a victim’s cookies with another set of legitimately obtained credentials.
When Arranging Cross-Domain Communications in JavaScript
 Do not use document.domain. Rely on postMessage(...) where possible and be sure to specify the destination origin correctly; then verify the sender’s origin when receiving the data on the other end. Beware of naïve substring matches for domain names: msg.origin.indexOf(".example.com") is very insecure.
 Note that various pre-postMessage SOP bypass tricks, such as relying on window.name, are not tamper-proof and should not be used for exchanging sensitive data.
When Embedding Plug-in-Handled Active Content from Third Parties
Consult the cheat sheet in Chapter 8 first for general advice.
 Flash: Do not specify allowScriptAccess=always unless you fully trust the owner of the origi- nating domain and the security of its site. Do not use this setting when embedding HTTP applets on HTTPS pages. Also, consider restricting allowFullScreen and allowNetworking as appropriate.
 Silverlight: Do not specify enableHtmlAccess=true unless you trust the originating domain, as above.
 Java: Java applets can’t be safely embedded from untrusted sources. Omitting mayscript does not fully prevent access to the embedding page, so do not attempt to do so.

When Hosting Your Own Plug-in-Executed Content
 Note that many cross-domain communication mechanisms provided by browser plug-ins may have unintended consequences. In particular, avoid crossdomain.xml, clientaccesspolicy .xml, or allowDomain(...) rules that point to domains you do not fully trust.
When Writing Browser Extensions
 Avoid relying on innerHTML, document.write(...), eval(...), and other error-prone coding patterns, which can cause code injection on third-party pages or in a privileged JavaScript context.
 Do not make security-critical decisions by inspecting untrusted JavaScript security con- texts, as their behavior can be deceptive.