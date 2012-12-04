Clippy - Helping you copy text to your clipboard
================================================

Clippy is a very simple Flash widget that makes it possible to place arbitrary
text onto the client's clipboard. Here is what Clippy looks like on GitHub:

![Clippy in action](http://img.skitch.com/20090213-cjiawnwig8udf5a6qf1c45cne8.png)

This is a fork of the original Clippy by Tom Preston-Werner. It modifies Clippy
to copy text by calling a JavaScript callback instead of passing the text to
copy as a flashvar. This allows for increased flexibility in retrieving the data
data to copy, and eliminates duplication of text (a possible concern if the text
to be clipped is lengthy).

Here is a sample Rails (Ruby) helper that can be used to place Clippy on a
page:

```ruby
def clippy(callback, parameter = nil, options = {})
  bgcolor   = options[:bgcolor] || '#fff'
  id        = options[:id] || 'clippy'
  css_class = options[:class] || 'clippy'
  html = <<-HTML
    <object classid="clsid:d27cdb6e-ae6d-11cf-96b8-44455354000"
            width="110"
            height="14"
            id="#{id}" class="#{css_class}">
    <param name="movie" value="/flash/clippy.swf" />
    <param name="allowScriptAccess" value="always" />
    <param name="quality" value="high" />
    <param name="scale" value="noscale" />
    <param NAME="FlashVars"
           value="callBack=#{callback}&parameter=#{parameter}" />
    <param name="bgcolor" value="#{bgcolor}" />
    <embed src="/flash/clippy.swf"
           width="110"
           height="14"
           name="clippy"
           quality="high"
           allowScriptAccess="always"
           type="application/x-shockwave-flash"
           pluginspage="http://www.macromedia.com/go/getflashplayer"
           FlashVars="callBack=#{callback}&parameter=#{parameter}"
           bgcolor="#{bgcolor}"
    />
    </object>
  HTML
  html.html_safe
end
```

Clippy (this fork, at least) accepts two parameters (as flashvars):

  * callBack *(required)* - A string representing a valid javascript function,
    such as <tt>function_name</tt> or <tt>MyApplication.function_name</tt>. Yes,
    "callBack" is a stupid capitalization, but "callback" is a keyword in Haxe.
  * parameter *(optional)* - A parameter to pass to aforementioned function

It will then call the function provided, and copy its return value to the
clipboard.

For example, to copy the HTML content of a div with a specific ID in Rails,
using jQuery and the helper above:

```html
<div id="my_awesome_content">
  This content is so <i>awesome</i>!
</div>
<script type="text/javascript">
  function fetch_content(id) {
    return $('#' + id).html();
  };
</script>

<%= clippy 'fetch_content', 'my_awesome_content' %>
```

Installation (Pre-Built SWF)
---------------------------

If you want to use Clippy unmodified, just copy `build/clippy.swf` to your
`public` directory or wherever your static assets can be found.

Installation (Compiling)
------------------------

In order to compile Clippy from source, you need to install the following:

* [haXe](http://haxe.org/)
* [swfmill](http://swfmill.org/)

The haXe code is in `clippy.hx`, the button images are in `assets`, and the
compiler config is in `compile.hxml`. Make sure you look at all of these to
see where and what you'll need to modify. To compile everything into a final
SWF, run the following from Clippy's root directory:

    swfmill simple library.xml library.swf && haxe compile.hxml

If that is successful, copy `build/clippy.swf` to your
`public` directory or wherever your static assets can be found.

Contribute
----------

If you'd like to hack on Clippy, start by forking my repo on GitHub:

http://github.com/mojombo/clippy

The best way to get your changes merged back into core is as follows:

1. Clone down your fork
1. Create a topic branch to contain your change
1. Hack away
1. If you are adding new functionality, document it in README.md
1. If necessary, rebase your commits into logical chunks, without errors
1. Push the branch up to GitHub
1. Send me (mojombo) a pull request for your branch

License
-------

MIT License (see LICENSE file)
