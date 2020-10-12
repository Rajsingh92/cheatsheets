---
author: Raj Singh Rajpoot
...

# DOM

#### Root Nodes
```javascript

document
document.documentElement
document.head
document.body
```

#### Parent Nodes
```javascript

parentNode
parentElement
```

#### Children Nodes
```javascript

childNodes
firstChild
lastChild
children
firstElementChild
lastElementChild
hasChildNodes
```

#### Sibling Nodes
```javascript

previousSibling
nextSibling
previousElementSibling
nextElementSibling
```

#### Create New Node
```javascript
createElement
createTextNode
cloneNode
node.textContent
node.innerHTML
```

#### Access Element
```javascript

getElementById
getElementByClassName
getElementByTagName
querySelector
querySelectorAll
```

#### Insert Node into DOM
```javascript

appendChild
insertBefore
replaceChild
insertAdjacentElement
insertAdjacentText
```

#### Remove Node From DOM
```javascript

remove
removeChild
```

#### About Node
```javascript

nodeName
nodeType
nodeValue
tagName
```

#### Popup
```javascript

alert
confirm
prompt
```

#### Timing Events
```javascript

setTimeout
setInterval
clearTimeout
clearInterval
```


#### Modifying Attributes
```javascript

hasAttributes
getAttribute
getAttributeNode
setAttribute
setAttributeNode
removeAttribute
removeAttributeNode
createAttribute
```

#### Modifying Classes
```javascript

className
classList
classList.add
classList.toggle
classList.contains
classList.replace
classList.remove
```

# Event Objects
#### Transition Event
```javascript

transitionend
propertyName
elapsedTime
```

#### Animation Event
```javascript

animationend
animationstart
animationiteration
animationName
elapsedTime
```

#### Focus Event
```javascript

focus
focusout
focusin
blur
```

#### UI Event
```javascript

abort
beforeunload
error
load
resize
scroll
select
unload
```

#### Keyboard Event
```javascript

keydown
keyup
keypress

keycode
key
code
ctrlkey
shiftkey
altkey
charcode
location
metakey
which
getmodifiedState
```

#### Mouse Event
```javascript

click
dbclick
mouseenter
mouseleave
mousemove
mousedown
mouseover
mouseup

relatedTarget
clientX
clientY
offsetX
offsetY
pageX
pageY
screenX
screenY
movementX
movementY
which
altkey
shiftkey
ctrlkey
metakey
getModifiedState
```

#### Wheel Event
```javascript

wheel
deltaX
deletaY
deltaNode
```

#### Input Event
```javascript

input
data
inputType
```

#### Drag Event
```javascript

drag
dragend
dragenter
dragleave
dragover
dragstart
drop
```

#### touch Event
```javascript

touchcancel
touchend
touchmove
touchstart

altkey
ctrlkey
metakey
shiftkey
touches
targetTouches
```
#### Hashchange Event
```javascript

haschange

newURL
oldURL
```

#### Page Transtion Event
```javascript

pagehide
pageshow

persisted
```

#### clipboard Event
```javascript

cut
copy
paste
```

# Objects

### String
```javascript

length
indexof
lastindexof
search
slice
substring
substr
replace
toUpperCase
toLowerCase
concat
trim
charAt
charCode
split

```

### Number
```javascript

toString
toExponential
toFixed
toPrecision
valueof
Number
parseInt
parseFloat

MAX_VALUE
MIN_VALUE
NaN
POSITIVE_INFINITY
NEGATIVE_INFINITY

```

### Array
```javascript

toString
join
pop
push
shift
unshift
length
length
delete
splice
concat
slice
sort
reverse
forEach
map
filter
reduce
reduceRight
every
some
indexof
lastindexof
```

### Math
```javascript

sqrt
abs
acos
acosh
asin
asinh
atan
sin
tan
log
cos
atan2
ceil
exp
floor
max
min
pow
random
round

E
LN2
LN10
LOG2E
LOG10E
trunc
PI
SQRT1_2
SQRT2

MAX_VALUE
MIN_VALUE
NAN
NEGATIVE_INFINITY
POSITIVE_INFINITY

toExponential
toFixed
toprecision
tostring
valueof
isNan
isInteger
isFinite
isSafeInteger

```

### Date
```javascript
var d = new Date();

d.getDate
d.getDay
d.getFullYear
d.getHours
d.getMilliseconds
d.getMinutes
d.getMonth
d.getSeconds
d.getTime
d.getTimezoneOffset

d.getUTCDate
d.getUTCday
d.getUTCFullYear
d.getUTCHours
d.getUTCMilliseconds
d.getUTCMinutes
d.getUTCMonth
d.getUTCSeconds

d.setDate
d.setFullYear
d.setHours
d.setMilliseconds
d.setMinutes
d.setMonth
d.setSeconds
d.setTime

d.setUTCDate
d.setUTCFullYear
d.setUTCHours
d.setUTCMilliseconds
d.setUTCMinutes
d.setUTCMonth
d.setUTCSeconds

d.toDateString
d.toISOString
d.toJSON
d.toLocaleDateString
d.toLocaleTimeString
d.toLocaleString
d.toString
d.toTimeString
d.toUTCString
UTC
valueof
```

# BOM
### Window
```javascript

window.innerHeight
window.innerWidth
window.open
window.close
window.resizeTo
window.moveTo
window.print
window.alert
window.confirm
window.prompt
window.screenLeft
window.screenTop
window.outerHeight
window.outerWidth
window.scrollBy
window.scrollTo
window.resizeBy
window.resizeTo
window.moveTo
window.moveBy
window.setInterval
window.setTimeout
window.clearInterval
window.clearTimeout
window.stop
```
### Screen
```javascript

screen.height
screen.width
screen.availHeight
screen.availWidth
```

### History
```javascript

window.history.back
window.history.forward
history.length
history.go
```

### Location
```javascript

location.hash
location.host
location.hostname
location.href
location.pathname
location.port
location.protocol
location.search
location.origin

location.assign
location.reload
location.replace
```

### Navigator
```javascript

navigator.appcodename
navigator.appName
navigator.appVersion
navigator.cookieEnabled
navigator.geolocation
navigator.language
navigator.online
navigator.platform
navigator.product
navigator.useragent
navigator.javaEnabled
```

### Console
```javascript

console.log
console.assert
console.clear
console.error
console.warn
console.table
console.time
```
