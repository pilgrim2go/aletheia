# Aletheia

(Pronounced *"ah-lee-thee-ah"*, from the Greek, *Αλήθεια*, meaning "truth")

This is a project that attempts to solve the problem of media verification and
information legitimacy on the internet.  In more relatable terms, Aletheia is
a way for news organisations to attach their reputations to their work, and for
that reputation to persist through the sharing of those works, giving human and
cultural context to social media data streams.

What follows is a break down of the problem Aletheia is trying to solve, how
present solutions are failing, why Aletheia is better, and finally a technical
break down of the simple process that underpins the whole process.


## The Problem

*"Fake News"* is an acknowledged problem in our social and political discourse,
but one that exists paradoxically in a time when legitimate news sources are
finding it more and more difficult to survive:

* Large organisations with seasoned reporters, field offices, and fact checkers
  are being squeezed out by unsourced bloggers with a fact checking staff of
  zero.
* New technologies that allow for the alteration of still images, audio and
  video are becoming more and more affordable to the broader public leading to
  more convincing fakes
* Public knowledge of these technologies has lead to a widespread skepticism of
  evidence in general.  Out of context of who is delivering the information,
  *all* evidence is considered suspect and therefore facts themselves,
  subjective.

In short, the world has changed in such a way that the public is now expected
to play the role of the discerning journalist, while simultaneously being faced
with advanced technologies designed to make that discernment as difficult as
possible.  We need the strengths of a free and responsible press more than
ever just as that press is disappearing.


## The Current Solution

Present attempts to tackle the problem are both expensive and highly technical,
opting only for more evidence (is the video real or fake?) rather than to take
advantage of any existing human capital.

The primary method for spotting fakes is machine learning algorithms: software
written to spot fakery generated by other software.  The trouble with this
approach of course is three fold:

* The amount of data flowing through the web every minute is far too great to
  ever be managed with machine learning algorithms.  There aren't enough CPUs
  on the planet to apply ML-based fakery tests to the video YouTube handles in
  a day, let alone every video, image, and audio file distributed on the
  various social networks every minute.
* Even if such computer power did exist, these processes are very expensive, a
  barrier likely to prevent most networks from implementation.
* The war can never be won.  For every fake caught, a better fake will be
  created.  This is an arms race that will never end.


## A Proposed Solution: Aletheia

Aletheia leverages our natural method for fake detection: *reputation*.

Rather than attempting to analyse the data presented, we verify the origin of
that data and allow the public to decide whether that source can/should be
trusted.

By way of example, consider a case where the BBC were to run a story with a
video of the British Prime Minister threatening war with the rest of European
Union.  Now consider the very same video, but presented by an organisation with
a much more dubious reputation like Info Wars.  Presentation of evidence is one
thing, but it's *who* is presenting that evidence that lends context to your
understanding of it -- your willingness to trust it.

This is  what Aletheia does: it helps the public to re-attach that degree of
trust to the information we're all consuming.


### How it Works

Aletheia isn't very complicated under the hood.  Indeed, there are only really
two parts to the whole process.  However the term "how it works" means
something very different to news media and politicians than it does to software
engineers.  To that end, this section is broken into two.

If you're here for the nuts & bolts, the technology of what's powering
Aletheia, you should probably skip down to the `More Technical`_ section.  If
however you just want a rough idea of what's involved, the `Less Technical`_
section is for you.


#### Less Technical

Aletheia is a three-part process: generation of keys, signing of files, and
verification of those files.  Importantly, the goal of this project is to make
signing & verification dead-simple and transparent.  Once setup, you shouldn't
even notice it's happening.


##### Generation

The power of Aletheia comes from a technology called *asymmetric cryptography*,
which is a fancy way of saying there's a lot of math that will let you take
ownership of something with one file that you keep to yourself, and other
people can verify that you created that thing so long as they have access to
another file that you share far-and-wide.

The concept of public & private keys is pretty complicated and there's really
no space to get into it all here.  Instead, here's all you really need to know
at this stage:

1. You create a pair of "key" files: one public, and one private
2. You keep the private key to yourself, and you share the public key with everyone.
3. You "sign" a file with your private key.
4. The world can then use your public key to verify that it came from you.

For more information on this, [this simple YouTube video](https://www.youtube.com/watch?v=GSIDS_lvRv4)
will get you started.

So to generate a pair of keys, you just need to run this command:

```bash
$ aletheia generate
```

This will create your public & private keys.  After that, you should put your
public key up on your website at a URL like `https://mywebsite.com/aletheia.pub`.


##### Signing

It's just like any other process of file creation.  You or your creative team
can do the work in Photoshop or MS Word, or literally any other software so
long as when they're finished, the file type is one of Aletheia's (growing)
list of supported formats.

Once you have the finished product, you simply run the `aletheia` program to
*"sign"* the file.  This will attach your authorship to the file, after which
you can distribute it just like you would anything else.

The signing step is about as simple as the generation step:

```bash
$ aletheia sign my_file.jpg
```

This process is designed to be simple & straight-forward, so a web development
team can easily integrate it into a website's underlying code: every file that
is served from your site can easily be signed with its origin with zero human
interaction required once it's setup.


##### Verification

Once someone gets a hold of your file(s) all they have to do to verify it came
from you is to feed that file into aletheia:

```bash
$ aletheia verify my_file.jpg
```

Aletheia is smart enough to figure out where to find your public key so the
end-user doesn't need to think about it.  The result is simple: Aletheia says
one of two things:

* This file came from `mywebsite.com`
* This file's origin can't be determined


#### More Technical

Aletheia is a simple asymmetric signature signing & verification program, that
has a single aim: verification of authorship/owernship.  Importantly, *it is
not DRM* and can be stripped from any file without repercussions.

The value is not in copy protection, but rather in relaying the *reputation of
the author* to the wider public when we are exposed to that file.  A media
organisation can sign any file it likes, but it can only sign its own
authorship to a file.  More practically put, Info Wars can copy a BBC stream
and re-sign it with their own, but they can't doctor a file and attach the
BBC's name to it.

##### Generation

Public & private key generation is done with a single command:

```bash
$ aletheia generate
```

This will place two files into `${ALETHEIA_HOME}` (`${HOME}/.config/aletheia` 
by default): `aletheia.pem` (your private key) and `aletheia.pub` (your public
key).  Keep the former private, and share the latter far-and-wide. Importantly,
place your public key at a publicly accessible URL so that when you sign a file
with your private key, it can be verified by reading the public key at that
URL.  This URL should be using HTTPS.


##### Signing

<p align="center">
  <img src="presentation/img/diagrams/sign-structure.png" alt="A typical file in two parts: header and body" /><br />
  Aletheia pulls the body of the file out and generates a signature of it with your private key
</p>

Nearly all files look like this: two parts, a header and a body:

* The body is where the actual data lives: pixel information for images, wave
  form data for audio, text for documents.  It's the majority of the file.
* The header is much smaller, consisting of information *about* the file.  For
  an image this would be photo data like focal length, height, and width, while
  for audio you might have the artist's name, and in video, the length and key
  frame information.

<p align="center">
  <img src="presentation/img/diagrams/sign-read.png" alt="A diagram of signature generation" /><br />
  Aletheia pulls the body of the file out and generates a signature of it with your private key
</p>

As the body is the data we care about -- the actual image, audio, video, or
text -- Aletheia reads *only this part* and generates a signature of this data
with a private key using the SHA512 hashing algorithm.  For this document,
we'll pretend that this file has a signature of `1234567890abcdef`.

Importantly, what constitutes the "body" of a file can sometimes be hard to
define.  In Aletheia, each file type has its own rules for defining this
portion.  For example, MP3's use the output of an FFmpeg command that strips
all header data, while JPEG files use Python's `.tobytes()` method.  As the
library of supported files expands, each of these formats will be documented.

<p align="center">
  <img src="presentation/img/diagrams/sign-write.png" alt="Inserting the signature & URL into the header" /><br />
  Tack the signature onto the header, along with where to find the public key
</p>

Now tht we have a signature, we take it and the URL for our public key and
combine these two values into a JSON string in the following format and insert
it into the file header:

```json
{"version":1,"signature":"1234567890abcdef","public-key":"https://mwebsite.com/aletheia.pub"}
```

We version the string so we can update the structure in the future without
breaking backwards compatibility, the signature is there for verification, and
the URL is necessary so the end-user knows where to find the public key.
Conveniently, this also allows us to to confirm that the origin of the file is
wherever the public key is stored.

<p align="center">
  <img src="presentation/img/diagrams/sign-final.png" alt="The final result: a file containing the origin of the file" /><br />
  The resulting file is a *little* bigger, but now contains the origin
</p>

This enlarges the file by approximately 2KB, but in doing this, we've just made
the file's origin verifiable.


##### Verification

Verification just the inverse of the above.  The only difference is that we use
a public key rather than your private one.

If you acquire a file that contains an Aletheia signature (as we generated
above) then all you need to do to verify it is run the `aletheia` program on
it:

```bash
$ aletheia verify my_file.jpg
```

Behind the scenes, Aletheia extracts the key URL and signature from the header:

<p align="center"><img src="presentation/img/diagrams/verify-extract.png" alt="Extract the signature & URL from the file" /></p>

With the URL in-hand, we then fetch the public key from the URL:

<p align="center"><img src="presentation/img/diagrams/verify-fetch.png" alt="Fetch the public key from the author server" /></p>

And finally, we use the public key to verify the signature against the file
body:

<p align="center"><img src="presentation/img/diagrams/verify-final.png" /></p>