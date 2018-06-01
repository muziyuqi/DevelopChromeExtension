# DevelopChromeExtension


1. File [Manifest]
context:
   {
    "name": "Getting Started Example",
    "version": "1.0",
    "description": "Build an Extension!",
    "manifest_version": 2
   }

2.Open the Extension Management page and Enable Developer Mode is ON.

3.drag folder to the page.

4.Add Instruction
  introduce a [background.js]
  
  those components must be registered in the [manifest]，like:
  ...
    "description": "Build an Extension!",
    "background": {
      "scripts": ["background.js"],
      "persistent": false
    },
    "manifest_version": 2
...
  

  [background.js]content:
  
  chrome.runtime.onInstalled.addListener(function() {
    chrome.storage.sync.set({color: '#3aa757'}, function() {
      console.log("The color is green.");
    });
  });
  
  Most APIs must be registered under the "permissions" field in the manifest,like:
  add:
    "permissions":["storage"],
    
  and then "Inspect views" becomes available with a blue link
    
5. Introduce a User Interface
  [popup] Interface
  create [popup.html] and its content are:
  
  <!DOCTYPE html>
  <html>
    <head>
      <style>
        button {
          height: 30px;
          width: 30px;
          outline: none;
        }
      </style>
    </head>
    <body>
      <button id="changeColor"></button>
    </body>
  </html>

  toolbar always include "default_popup" and "default_icon"
  add "page_action" to manifest:
  
     "page_action": {
      "default_popup": "popup.html",
    }, 
  
  add  ["default_icon":]  in [page_action]
  "default_icon": {
        "16": "images/get_started16.png",
        "32": "images/get_started32.png",
        "48": "images/get_started48.png",
        "128": "images/get_started128.png"
      }
      
   add ["icons"] in [manifest]
       "icons": {
      "16": "images/get_started16.png",
      "32": "images/get_started32.png",
      "48": "images/get_started48.png",
      "128": "images/get_started128.png"
    },

  add "declarativeContent" to "permissions" in [manifest]
  like:
    "permission":["declarativeContent","storage"].
    
 6. create "popup.js" 
 
      let changeColor = document.getElementById('changeColor');

      chrome.storage.sync.get('color', function(data) {
        changeColor.style.backgroundColor = data.color;
        changeColor.setAttribute('value', data.color);
      });
      
      add  <script src = "popup.js"></script>  below <button ... ></button> in popup.html
      
7.Layer Logic
  add code in popup.js
  
    changeColor.onclick = function(element) {
    let color = element.target.value;
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
      chrome.tabs.executeScript(
          tabs[0].id,
          {code: 'document.body.style.backgroundColor = "' + color + '";'});
    });
  };

  add "activeTab" to "permissions" in [manifest]
  
  8. Give Users Options
    create options.html

<!DOCTYPE html>
  <html>
    <head>
      <style>
        button {
          height: 30px;
          width: 30px;
          outline: none;
          margin: 10px;
        }
      </style>
    </head>
    <body>
      <div id="buttonDiv">
      </div>
      <div>
        <p>Choose a different background color!</p>
      </div>
    </body>
    <script src="options.js"></script>
  </html>    
    
 add "options_page":"options_html" to [manifest]
 
 
  create options.js
  
  let page = document.getElementById('buttonDiv');
  const kButtonColors = ['#3aa757', '#e8453c', '#f9bb2d', '#4688f1'];
  function constructOptions(kButtonColors) {
    for (let item of kButtonColors) {
      let button = document.createElement('button');
      button.style.backgroundColor = item;
      button.addEventListener('click', function() {
        chrome.storage.sync.set({color: item}, function() {
          console.log('color is ' + item);
        })
      });
      page.appendChild(button);
    }
  }
  constructOptions(kButtonColors);


