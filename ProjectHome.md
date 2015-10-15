Implementation of an html and javascript context scanner with no lookahead. Its purpose is to scan an html document and provide context information at any point within the input stream. An example of a user of this scanner would be an auto escaping templating system, which would require html context information at very specific points within the html stream. The implementation is based on a simplified state machine of HTML4.1 and javascript. The code also contains C++ and python bindings.

Here is a very simple C program that illustrates how to use a small subset of the parser API. It expands each $ character it finds to a string containing the current tag and attribute name.

```
#include <stdio.h>
#include <streamhtmlparser/htmlparser.h>

int main(void) {
  unsigned int getchar_ret;
  htmlparser_ctx *parser = htmlparser_new();

  while((getchar_ret = getchar()) != EOF) {
    char c = (char)getchar_ret;

    /* If we received a '$' character, we output the current tag and attribute
     * name to stdout. */
    if (c == '$') {
      printf("[[ ");
      if (htmlparser_tag(parser))
        printf("tag=%s ", htmlparser_tag(parser));
      if (htmlparser_attr(parser))
        printf("attr=%s ", htmlparser_attr(parser));
      printf("]]");

    /* If we read any other character, we pass it to the parser and echo it to
     * stdout. */
    } else {
      htmlparser_parse_chr(parser, c);
      putchar(c);
    }
  }
}
```


Given the following input:

```
<html>
  <body $>
    <title> $ </title>
    <a href="$" alt="$"> url </a>
  </body>
</html>
```


It will output the following annotated version:

```
<html>
  <body [[ tag=body ]]>
    <title> [[ tag=title ]] </title>
    <a href="[[ tag=a attr=href ]]" alt="[[ tag=a attr=alt ]]"> url </a>
  </body>
</html>
```

For more information on the supported API please consult the header file [streamhtmlparser/htmlparser.h](http://code.google.com/p/streamhtmlparser/source/browse/trunk/src/streamhtmlparser/htmlparser.h).