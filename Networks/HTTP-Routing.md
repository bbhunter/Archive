#  HTTP Routing 

Traditionally web servers would have directory structures where the "web root" is the base directory. On linux it is located under /var/www/html/. If files are added here they are hosted by the web server:
- The .index files contain default content, these are the default page browsers route to.  If an .index file is unspecified the web server may return a directory listing.
- Robots.txt allows specifying of the non-crawlable pages.
- Humans.txt is newer convention listing web content creators.
- sitemap.xml is antithesises to robots.txt

Modern sites INSTEAD of files in a directory structure would define a URL at site/ to respond with a certaon resource.To reiterate slightly, site/ is not a file, it would be a full name of a resource and not actually a path to a file. No files hosting content - we NEED to know the full name of the resource in order to access it: /user be a resource but not /users/ Resources can be coded to be access via a particular method. This makes resource enumeration more time consuming.