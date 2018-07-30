# Fron-End Techniques


**A collection of Front-end techniques** using ES6 and the [JavaScript Library to target DOM elements](#markdown-header-javaScript-library-to-target-dom-elements )


• [`JavaScript library to target DOM elements`](#markdown-header-javaScript-library-to-target-dom-elements)

• [`Simple cycled image carousel`](#markdown-header-simple-cycled-image-carousel)

• [`Simple image lazyload`](#markdown-header-simple-image-lazyload)

• [`Modal window – Lightbox`](#markdown-header-modal-window-lightbox)

• [`Detect whether a given element is on viewport`](#markdown-header-detect-whether-a-given-element-is-on-viewport)

• [`Scroll-animate to an element`](#markdown-header-scroll-animate-to-an-element)

• [`Script to gather metadata for sharing links`](#markdown-header-script-to-gather-metadata-for-sharing-links)


___

## JavaScript Library to target DOM elements 
###
___

```javascript
// Target a single element
select = el  => document.querySelector(el),
// Target multiple elements needs a for loop to iterate through all of them
selectAll = els => document.querySelectorAll(els),
// Targets single ID 
getId = id  => document.getElementById(id),
// Capitalise words
caps = str => str.charAt(0).toUpperCase() + str.slice(1)
```
___
## Simple cycled image carousel
###
___


###*HTML*
###

```html
<ul id='slides'>
  <li class='slide showing'>Image 1</li>
  <li class='slide'>Image 2</li>
  <li class='slide'>Image 3</li>
</ul>
```

###*CSS*
###

```css
#slides {
  position: relative;
  height: 300px;
  padding: 0px;
  margin: 0px;
  list-style-type: none;
}

.slide {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  z-index: 1;
  transition: opacity 3s;
  background: no-repeat center / cover;	
}

slide:nth-of-type(1) { background-image: url('../img/hero/hero_1.jpg') }
slide:nth-of-type(2) { background-image: url('../img/hero/hero_2.jpg') }
slide:nth-of-type(3) { background-image: url('../img/hero/hero_3.jpg') }
		
.showing {
  opacity: 1;
  z-index: 2;
}

```


####*JavaScript*
####

```javascript

simpleCarousel: () => {
  const slides = selectAll('#slides .slide')
  let index = 0
  	
  setInterval(() => {
    slides[index].className = 'slide'
    index = (index + 1) % slides.length
    slides[index].className = 'slide showing'
  }, 4000)
}

```
___
## Simple image lazyload
###
___

####*JavaScript*
####


```javascript
lazyLoad:() => {
  select('.image').setAttribute('src', select('.image').getAttribute('data-src'))
  select('.image').onload = () =>  select('.image').removeAttribute('data-src')
}
```

___
## Modal Window – Lightbox
###
___

####*HTML*
####

```HTML

<!-- Your modal CTA -->
<div class='btn'>CTA</div>

<!-- Your modal wrapper and content -->
<div class='modal'>
	<div class='modal-content'>
		<!-- Closing button normally absolute to the modal content -->
		<div class='close'>Your closing icon</div>
		<!-- Modal content -->
		<p>Content</p>
	</div>
</div>
```
####*CSS*
####
```css
.modal {
  display: none;
  background-color: rgba(0,0,0,0.6);
  position: fixed;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
}

.modal.open {  display: block }

.modal-content { 
  z-index: 1000;
  position: absolute;
  left: 50%;
  top: 50%;
  width: 100%;
  border-radius: 8px;
  max-width: 300px;
  background-color: #fff;
  transform: translate(-50%, -50%);
  padding: 4em;
}

```

####*JavaScript*
####
```javascript

modalWindow: () => {    
  // Store the modal wrapper and closing fn
  const modal = select('.modal'), 
        closeModal = () => modal.classList.remove('open')
		
  // Set the info button as our click event to open modal window
  select('.btn').addEventListener('click', () => modal.classList.add('open'))

  // Remove modal window when clicking everywhere but the modal content itself
  modal.addEventListener('click', () => { 
    closeModal()
    select('.close').onclick = () => closeModal()
    select('.modal-content').onclick = e => e.stopPropagation()
  })      
}
```
___
## Detect whether a given element is on viewport
###
___

####*JavaScript*
####

```javascript

// Detect element is on viewport
viewPort(el) {
  const rect = el.getBoundingClientRect(), offset = rect.height,
        viewHeight = Math.max(document.documentElement.clientHeight, window.innerHeight)
        
  return viewHeight > rect.top + viewHeight - offset
},
    
```
___
## Scroll-animate to an element
###
___

####*JavaScript*
####

```javascript
// Function to smooth scroll to a given element 
animate: (to, time) => {
  // Optional speed defaults to a second if not present
  time = typeof time === 'undefined' ? 1000 : time
  let doc = document.scrollingElement || document.documentElement
  if (!doc) return
  const start = new Date().getTime(), timer = setInterval(() => {
    let step = Math.min(1, (new Date().getTime() - start) / time)
    doc.scrollTop = (window.pageYOffset + step * (to.offsetTop - window.pageYOffset))
    if (step === 1)  clearInterval(timer)
  }, 25)
  doc.scrollTop = window.pageYOffset
}
```
___
## Script to gather metadata for sharing links
###
___

####*HTML*
####

```HTML
<ul class="sharelinks">
  <li>
    <a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=SBLINK" title="Share on Facebook"></a>
  </li>
  <li>
    <a target="_blank" href="https://twitter.com/intent/tweet?text=Twitter prefill: %0ASBLINK" title="Share on Twitter"></a>
  </li>
  <li>
    <a target="_blank" href="https://www.linkedin.com/shareArticle?mini=true&url=SBLINK&title=SBTITLE&summary=SBDESC&source=" title="Share on LinkedIn"></a>
  </li>
</ul>
```

####*JavaScript*
####

```Javascript
// Automated script that gathers metadata from markup to social sharing links
setSharing: () => {
  // Function to get attributes from metadata
  const getMetaContentByName = (name, attrtype, content) => {
    content = content == null ? 'content' : content
    const ret = select('meta[' + attrtype + '=\'' + name + '\']').getAttribute(content)
    return ret.replace(/ /gi, '%20');
  }
  // Looks for the words SBLINK, SBTITLE and SBDESC and replaces with existing metadata
  each(selectAll('.social li'), els => {
    let txt = els.innerHTML.replace(/SBTITLE/gi, getMetaContentByName('og:title', 'property'))
    txt = txt.replace(/SBLINK/gi, escape(window.location.href))
    txt = txt.replace(/SBDESC/gi, getMetaContentByName('description', 'name'))
    els.innerHTML = txt
  })
}
```