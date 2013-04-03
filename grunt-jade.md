# Grunt-jade

### options

* client: false
  Create templates to be used from server.
  Useful for .html template files and static pages.

* extension: '.custom'
  Define custom extensions the compiled templated will be used with.
  To have no extension just do
    extension: ''

* locals: { title: "This is passed to template" }
  Define variables to be passed to templates.
  Variables will be accessed by #{title} in template files.

  Locals can be dynamic. Use a function to return an object.
  locals: { title: new Date() }

* basePath: 'path/to'
  Mimic directory structure from your source templates into
  the destination from a given path.
  source: 'path/to/source/dir'
  dest: 'wwww'
  basePath: 'path/to'
  => compile: 'www/source/dir/'


### Template files

* include path/to/templade.jade
  Include a jade template file/html/css in the current template.

* #{content}
  Pas the content (compiled?) as a variable to a template.

* block & extends
  How to:
    1. create a file, within print "block <name>"
    2. create a file, on top print "extends <filename>", within print "block <name>"
  Default is replace, but prepend, append also possible
  Can be have a default value set

* extends _layout
  block main

