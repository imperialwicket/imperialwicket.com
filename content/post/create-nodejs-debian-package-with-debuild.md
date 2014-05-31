{
  "title": "Create nodejs debian package with debuild",
  "description": "Create nodejs debian package with debuild",
  "date": "2013-08-07",
  "url": "create-nodejs-debian-package-with-debuild/",
  "type": "post",
  "tags": [
    "nodejs",
    "debian"
  ]
}
I ran into a couple issues when building a debian package for nodejs. 

*   Python build processes create a bunch of *.pyc files that cause problems when re-running debuild after errors
*   The python configure script doesn't compare well with more classic configure scripts (notably --build vs --dest-cpu)
*   Make encounters issues when passing standard params
*   Auto test runs into problems with server validation when run via debuild

I took the quick and easy solution in all cases. I didn't resolve the *.pyc issue, I simply started with a clean directory for each pass. The rest of the solutions came by making a barebones build run via debian/rules:

<pre>
#!/usr/bin/make -f
%:
        dh $@

override_dh_auto_configure:
        ./configure --prefix /usr

override_dh_auto_make:
        make

override_dh_auto_test:
</pre>

This forces debuild to use the simple 'make' command and pass no additional parameters, to skip the auto_test phase, and to use the simple './configure --prefix /usr' configure command instead of its usual configure.

Other than the specifics of the debian/rules file, you can follow [general debuild instructions](https://wiki.debian.org/IntroDebianPackaging).
