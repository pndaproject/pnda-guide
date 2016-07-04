# Instructions

Here are some instructions for using gitbook to build the PNDA guide. 

## Website

Check out the `pnda-guide` repo and change to that directory. 

Follow the gitbook [Installation and Setup](http://toolchain.gitbook.com/setup.html) guide. Basically, to install gitbook:

```
npm install gitbook-cli -g

gitbook init
```

To build the HTML website:

```
gitbook build
```

## PDF

To build a PDF, follow the [eBook and PDF](http://toolchain.gitbook.com/ebook.html) instructions. This seems to require a Mac. 

Download the [Calibre](https://calibre-ebook.com/download) application and copy it to `/Applications`. Then make a symbolic link so gitbook can find it:

```
sudo ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin
```

To build a PDF:

```
gitbook pdf ./ ./pnda-guide.pdf
```

## Build script

To build everything, just run:

```
scripts/build.sh
```

This will build the website, compress it to a `pnda-guide.zip` archive, and build the `pnda-guide.pdf` file.

## Repositories

The `repos` directory contains a copy of the README file from every repository in the distribution. When the README files have changed, this command will copy them into the repos directory, along with any linked images:

```
scripts/copy-repos.sh
```

## Uploading

To upload the built guide to the website:

```
scripts/upload.sh pnda-website 0.0.6 staging
scripts/upload.sh pnda-website 0.0.6 production
```

In this case, `pnda-website` should be a host defined in `~/.ssh/config`.
