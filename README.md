Docker Thumbor container
========================

None of the containers I could find online seemed to be working properly with open CV and an injectable security key. This one is based on debian jessie and has those features, plus aggressive face detection and filesystem-based cache. Face detection for cropping is our primary use case, so decisions in this system are optimized for that.

TODOS include using redis for the cache via an ENV var so that this is more load-balancable.

Usage:
------

The container starts on port 80 serving HTTP traffic by default.

    $ docker run -p 80:80 mavenclinic/thumbor
    $ wget http://dockerip:80/unsafe/300x300/smart/<some_image_URL>

Adding a secret key (and turning off unsafe URLs) is as simple as:
    $ docker run -p 80:80 -e SECRET_KEY=foobar mavenclinic/thumbor


Configuration
-------------

The container comes with a default configuration, that includes:

- Using the filesystem storage, in ``/srv/thumbor/storage``.
- Unsafe urls are enabled.

To customize the configuration, you should share in a config file to `/etc/thumbor.conf` using any way to get files on there, including docker's `-v` option or `--volumes-from` for example. You should also override the default command of `/init` or put your secret key in your thumbor.conf instead of the ENV, because the `/init` script will append safe URLs only and your secret key at the end of the file if that is present.


Volumes
-------

/srv/thumbor/storage
    In the default configuration, this is the image cache.


Environment variables
---------------------

SECURITY_KEY
    Sets the security key and disallows **"unsafe"** urls.


Note
----

This message:

"libdc1394 error: Failed to initialize libdc1394"

from OpenCV seems to be a warning only.


Credits
----
This was heavily inspired by https://github.com/miracle2k/dockerfiles/tree/master/thumbor.
