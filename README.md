(function(window, document, version, callback) {
    var j, d;
    var loaded = false;
    if (!(j = window.jQuery) || version > j.fn.jquery || callback(j, loaded)) {
        var script = document.createElement("script");
        script.type = "text/javascript";
        script.src = "/media/jquery.js";
        script.onload = script.onreadystatechange = function() {
            if (!loaded && (!(d = this.readyState) || d == "loaded" || d == "complete")) {
                callback((j = window.jQuery).noConflict(1), loaded = true);
                j(script).remove();
            }
        };
        (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(script);
    }
})(window, document, "1.3", function($, jquery_loaded) {
    // Widget code here
});

Coffeescript version:

main = (window, document, version, callback) ->
  [j, d, loaded] = [null, null, false]
  if !(j = window.jQuery) || version > j.fn.jquery || callback(j, loaded)
    script = document.createElement("script");
    script.type = "text/javascript";
    script.src = "//ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js";
    script.onload = script.onreadystatechange = () ->
      if !loaded && (!(d = this.readyState) || d == "loaded" || d == "complete")
        callback((j = window.jQuery).noConflict(1), loaded = true)
        j(script).remove();
    (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(script)
 
main window, document, "1.7", ($, jquery_loaded) ->
  # Widget code here
  
http://alexmarandon.com/articles/web_widget_jquery/ :
(function() {

// Localize jQuery variable
var jQuery;

/******** Load jQuery if not present *********/
if (window.jQuery === undefined || window.jQuery.fn.jquery !== '1.4.2') {
    var script_tag = document.createElement('script');
    script_tag.setAttribute("type","text/javascript");
    script_tag.setAttribute("src",
        "http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js");
    if (script_tag.readyState) {
      script_tag.onreadystatechange = function () { // For old versions of IE
          if (this.readyState == 'complete' || this.readyState == 'loaded') {
              scriptLoadHandler();
          }
      };
    } else { // Other browsers
      script_tag.onload = scriptLoadHandler;
    }
    // Try to find the head, otherwise default to the documentElement
    (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(script_tag);
} else {
    // The jQuery version on the window is the one we want to use
    jQuery = window.jQuery;
    main();
}

/******** Called once jQuery has loaded ******/
function scriptLoadHandler() {
    // Restore $ and window.jQuery to their previous values and store the
    // new jQuery in our local jQuery variable
    jQuery = window.jQuery.noConflict(true);
    // Call our main function
    main(); 
}

/******** Our main function ********/
function main() { 
    jQuery(document).ready(function($) { 
        // We can use jQuery 1.4.2 here
    });
}

})(); // We call our anonymous function immediately

-----------------------------------------------
Can you use document.write() to optionally add the jQuery script to the page? That should force jQuery to load synchronously. Try this:

<script type="text/javascript" charset="utf-8">
// <![CDATA
 if (typeof jQuery === 'undefined') {
  document.write('<script src="{{ URL }}/jquery.js"><' + '/script>');
 }
// ]]>
</script>
<script type="text/javascript" src="{{ URL }}/widget.js"></script>
If you want to do the jQuery check inside your widget script then I believe the following works cross-browser:

(function() {
 function your_call($) {
  // your widget code goes here
 }
 if (typeof jQuery !== 'undefined') your_call(jQuery);
 else {
  var head = document.getElementsByTagName('head')[0];
  var script = document.createElement('script');
  script.type = 'text/javascript';
  script.src = '{{ URL }}/jquery.js';
  var onload = function() {
   if (!script.readyState || script.readyState === "complete") your_call(jQuery);
  }
  if ("onreadystatechange" in script) script.onreadystatechange = onload;
  else script.onload = onload;
  head.appendChild(script);
 }
})()
