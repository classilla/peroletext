# PeroleText

Copyright © 2022 Cameron Kaiser.  
All rights reserved.  
Released under the Perl Artistic License 2.0.

## What is it?

PeroleText is a set of two tools to convert to and from
[ProleText](https://www.templetons.com/tech/proletext.html), an early
text-based markup system circa 1995 that enabled HTML-like formatting encoded
via trailing whitespace and certain inline sequences. This markup was generally
invisible, allowing documents to appear largely as legible plain text to basic
readers, but convertable into more-or-less full HTML (of the time) with a
sufficiently advanced viewer.

For more information on ProleText's history and a comparison with later
text markup systems like Markdown, see
[this blog post](http://oldvcr.blogspot.com/).

## Usage

The tools allow conversion from ProleText to HTML (`pt2html`), and
HTML to ProleText with lossy conversion (`html2pt`). They are
currently supported on Perl 5.8 or better, though they may work on earlier
versions. No Perl modules are required to use them other than the basic
Perl interpreter.

To use them as command line utilities, simply `chmod +x` either
`pt2html` or `html2pt`, and pipe data to them:

    % ./pt2html example.txt
    % ./html2pt test.html
    % curl http://oldvcr.blogspot.com/ | ./html2pt | ./pt2html

This document, in fact, was converted with `html2pt` and
Html2Markdown.

Both tools accept a `-debug` option, allowing you to see the actual
ProleText tuples and the view of the HTML parser, respectively. Try 
`./pt2html -debug README.txt` to see the tuples generated for this
very document.

To use them as Perl libraries, you can either `require "pt2html.pl"`
or `require "html2pt.pl"` (which are symlinks), or
`use PeroleText` or `use PeroleHTML` (which are also
symlinks). No modification is needed; the files will do "the right thing"
based on the context you use them in.

The PeroleText converters are line-oriented; you feed lines of text to them
until you're out of data. Both have just three methods, differing only in the
arguments to the constructor:

     my $p = new PeroleText([$debug]);
     print $p->proline($string);
     print $p->done;

     my $p = new PeroleHTML([$debug,][$img]);
     print $p->proline($string);
     print $p->done;

The `$debug` argument (0=false, 1=true) indicates whether to run the
converter in debug mode. For ProleText to HTML, this emits the tuples. For HTML
to ProleText, this emits the view of the HTML parser. The second argument, only
for `PeroleHTML`, says whether to always replace images with their
`ALT` text (if true, `<IMG>` tags are used as is).
Any unspecified argument to the constructor is treated as false.

Then, for each line of data, call the method `proline` (this is a
specific pun I refuse to explain but Canadian Commodore users should get
— hint: Jim Butterfield and Steve Punter), which will return a string
you can print or store. This string may not include all of the elements you
passed it, particularly in the HTML converter, so when you're out of data call
the method `done` to return anything left over in the object's
buffer. The object instance is not reusable when the conversion is complete;
destroy it or let it go out of scope after calling `done`, and make
a new instance if you need to do a new operation. Although there are other
"secret" instance methods available for these objects, they should be
considered neither public, stable nor supported.

## Pull requests and issues

This is a toy project. I can't imagine you'd use this in a production
environment, but if you have any ideas about doing so, slowly back away
from the keyboard and think long and hard about the other bad choices
you've made in life. Lots of sites probably won't work with the
HTML-to-ProleText converter (in fact, those without significant text content
will *definitely* not work). Issues that are not correctable may be
closed as out of scope. Pull requests are accepted whenever I get around to
them, which may be years from now.

