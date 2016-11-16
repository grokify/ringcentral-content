# Using URI Scheme with RingCentral Mobile and Desktop Softphones

The RingCentral softphone supports 3 different URI schemes:

| Scheme | Description |
|--------|-------------|
| `rcmobile` | RingCentral's custom scheme for both mobile and desktop apps |
| `tel` | IETF standard [RFC-3966](https://tools.ietf.org/html/rfc3966) |
| `callto` | Common industry scheme |

This article covers:

1. Supported URI Schemes
2. Supported Phone Number Formats
3. Example Use in Web Apps

## Supported URI Schemes

### rcmobile URI Scheme

The `rcmobile` scheme is specific to RingCentral and you can be sure it will launch the RingCentral softphone app. To initiate an outbound call you can use a URL like the following:

* `rcmobile://call?number=16501112222`

### tel URI Scheme

The `tel` scheme is an IETF standard and may be used by a variety of applications. If an application has general support for click-to-deal that is not RingCentral specific, this can be used when the RingCentral app has been registered to respond for that scheme with the operating system. You can access this scheme using the following:

* `tel:1-650-111-2222`
* `tel:16501112222`

### callto URI Scheme

The `callto` scheme is a popular industry scheme that some applications may implement. The RingCentral softphone also supports registering for this scheme. To use this scheme, implement the following URL format:

* `callto:16501112222`

## Supported Phone Number Formats

RingCentral supports [E.164 format](https://en.wikipedia.org/wiki/E.164) and E.164 format without the leading `+`.

## Example Use in Web Apps

### Standard Approach

```html
<!-- rcmobile is only used by RingCentral -->
<a href="rcmobile://call?number=16501112222"&gt;1-650-111-2222&lt;/a>
<!-- tel can be used by may be used by multiple apps -->
<a href="tel:1-650-111-2222"&gt;1-650-111-2222&lt;/a>
<a href="tel:16501112222"&gt;1-650-111-2222&lt;/a&gt;
```

### Google Chrome

Chrome requires execution of JavaScript to initiate a URI scheme call. The following JavaScript code can be used:

```javascript
// Use the following for Google Chrome only
var w = (window.parent)?window.parent:window;
w.location.assign('rcmobile://call?number=16501112222');
```

For more information, see [http://stackoverflow.com/questions/2330545/](http://stackoverflow.com/questions/2330545/)
