=======
aioipfs
=======

:info: Asynchronous IPFS_ client library

**aioipfs** is a python3 library providing an asynchronous API for IPFS_.
Supported python versions: *3.6*, *3.7*, *3.8*, *3.9*

**NOTE:** This is a separate fork of the Original_ created by @pinnaculum (formerly ``cipres``).
This fork is maintained by `Privex Inc.`_ (primarily by @someguy123) - a Belizean server hosting + technology company.

.. image:: https://github.com/pinnaculum/aioipfs/workflows/aioipfs-build/badge.svg
    :target: https://github.com/pinnaculum/aioipfs

.. _Original: https://github.com/pinnaculum/aioipfs
.. _Privex Inc.: https://www.privex.io/

Installation
============

.. code-block:: shell

    pip3 install privex-aioipfs
    # Or if you use pipenv
    pipenv install privex-aioipfs


Usage examples
==============

Get an IPFS resource
--------------------

.. code-block:: python

    import sys
    import asyncio

    import aioipfs

    async def get(ipfshash):
        client = aioipfs.AsyncIPFS()
        await client.get(ipfshash, dstdir='.')
        await client.close()

    loop = asyncio.get_event_loop()
    loop.run_until_complete(get(sys.argv[1]))
    loop.close()

Add some files
--------------

This example will import all files and directories specified on the command
line. Note that the **add** API function is an asynchronous generator and
therefore needs to be used with the *async for* syntax.

.. code-block:: python

    import sys
    import asyncio

    import aioipfs

    async def add_files(files):
        client = aioipfs.AsyncIPFS()

        async for added_file in client.add(*files, recursive=True):
            print('Imported file {0}, CID: {1}'.format(
                added_file['Name'], added_file['Hash']))

        await client.close()

    loop = asyncio.get_event_loop()
    loop.run_until_complete(add_files(sys.argv[1:]))
    loop.close()

Pubsub service
--------------

.. code-block:: python

    async def pubsub_serve(topic):
        async with aioipfs.AsyncIPFS() as cli:
            async for message in cli.pubsub.sub(topic):
                print('Received message from', message['from'])
                await cli.pubsub.pub(topic, message['data'])

Features
========

Async file writing on get operations
------------------------------------

The **aiofiles** library is used to asynchronously write data retrieved from
the IPFS daemon when using the */api/v0/get* API call, to avoid blocking the
event loop. TAR extraction is done in asyncio's threadpool.

Requirements
============

- Python >= 3.6, <= 3.9
- aiohttp_
- aiofiles_
- yarl_

.. _aiohttp: https://pypi.python.org/pypi/aiohttp
.. _aiofiles: https://pypi.python.org/pypi/aiofiles
.. _yarl: https://pypi.python.org/pypi/yarl
.. _IPFS: https://ipfs.io

License
=======

**aioipfs** is offered under the GNU Lesser GPL3 (LGPL3) license.
