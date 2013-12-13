Web Development With Jade
=================
If you're reading this, you've been invited to review *Web Development With Jade* before its public release.

##The Book

About 4 months ago, I started planning this book to provide a solid source of documentation for people looking to learn how to use Jade. After countless delays, I am happy to say it is nearing completion.

The next step is to have members of the community read through the book, make sure I've explained everything correctly, and in general: make this book awesome. Rather than using the editing system that [Packt](http://www.packtpub.com/) normally employs (which is a terrible cycle of emailing Word docs between editors), I've written the book in Markdown and switched to using git for revision control.

So, as a reviewer, I ask you to do the following things:
 - [Read](http://hhsnopek.github.io/jade-book/) through the book
 - Open issues (via the Github issue tracker) for anything that doesn't look right, or could be better
 - Don't share the book with "the rest of the world". I'm like 98% sure that the contract I signed says I'm not supposed to post this on torrent sites and what-not, so I ask that you don't share it either.
 - **BONUS**: If you're feeling *really* ambitious, open a PR to add useful content to the book, or make changes.

##The Source

As I mentioned above, the whole book is written in Markdown, and (of course) a little Jade to hold it together. Each chapter has a file in `/views` (`_ch1.md` to `_ch9.md`). You can read these directly on GitHub at http://hhsnopek.github.io/jade-book/. Or, if you want to modify the book, you can use [roots](http://roots.cx) to compile it:

```bash
npm install roots -g
git clone https://github.com/HHSnopek/jade-book.git
cd jade-book
roots watch
```

...and a browser window should open for you automatically
