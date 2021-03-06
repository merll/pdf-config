# PDF-Config
A simple, easy-to-script tool for merging multiple PDF files into one
document using a YAML configuration file.

The program is written in Python, using the
[PyPDF4](https://github.com/claird/PyPDF4) library.

## Purpose
There are many good utilities for splitting and merging PDF files.
For instance, if you prefer one with a GUI,
[PDF Arranger](https://github.com/jeromerobert/pdfarranger)
is a good choice. However, I had slightly different requirements:
* Composing several documents from a similar set of files, but
  each time with slight modifications.
* Creating a structure of bookmarks, so that it is easier to
  navigate the larger document.
* Original PDF features such as hyperlinks and the page orientation
  should remain intact.

## Features
* Simple YAML configuration file structure
* Adding PDF metadata
* Creating bookmarks, also with nested structures
* Checking the version of merged PDF files and setting the minimum in the
  output PDF

## Installation
### Prerequisites
This program requires Python 3.6+.

#### Windows
Download and install from https://www.python.org/downloads/.

#### MacOS X
The best is to use [Homebrew](https://brew.sh/) and install using
```sh
brew install python
```
#### Linux
Install Python using your distribution's package manager.
Usually the package is named ``python3``.

### Virtual environment
It is not strictly required but strongly advised to create a
*virtual environment* for installing Python packages for a specific
purpose.

Create one in any preferred location using
```sh
virtualenv -p python3 pdfconfig
```
The last argument can also be changed if preferred. Then activate the
new environment:
```sh
source pdfconfig/bin/activate
```

### Installing this tool
```sh
pip install pdf-config
```

## Usage
### Configuration file
A configuration file is set up in
[YAML syntax](https://en.wikipedia.org/wiki/YAML#Syntax)
with the following components:
```yaml
metadata:  # Optional
  title: My document title
  author: ME
  # Additionally supported:
  # creator, keywords, producer, subject

  # Hard-coded version to set in the header. Set to 'auto' or leave out
  # entirely for using the maximum version of all input documents. 
  version: '1.6'

paths:  # Optional
  # List of paths to look up any files that do not contain a path
  # specification. The current directory is checked first, then the following
  # directories are checked in that order.
  - ~/my-pdfs  # User home directory can be referred to.
  - $ADDITIONAL_PDF_PATH  # Environment variables are also supported.

contents:  # The only required element
  # Each list entry can contain any of the following:
  # bookmark: The bookmark title
  # document: The name (and path) of the input file.
  # contents: An additional list of contents. Any bookmarks in this sub-structure
  #   are placed under this bookmark, if present.
 
  - bookmark: First  
    document: first.pdf
  - bookmark: Second
    contents:
      - bookmark: Second doc 1
        document: sd1.pdf
      - bookmark: Second doc 2
        document: sd2.pdf
  - bookmark: Third
    document: ~/pdfpath/third.pdf  # Relative and absolute paths are supported.
    contents:
      - bookmark: Third doc 1
        document: $PDF_T1  # Environment variables are also expanded.
      - bookmark: Third doc 2
        document: $PDF_T2
```
The order of ``metadata``, ``paths``, and ``contents`` above is not relevant.
The resulting PDF bookmark structure will be
```
|-First
|
|-Second
| |-Second doc 1
| |-Second doc 2
|
|-Third
  |-Third doc 1
  |-Third doc 2
```

``Second`` points to the same page as ``Second doc 1``, whereas ``Third``
and ``Third doc 1`` point to different pages, since ``Third`` inserts pages on
its own.

### Start
With the configuration stored in ``sample.yaml`` and the PDF files in place,
start the merging process by running
```sh
pdfconfig sample.yaml
```
This will merge the listed PDF files into `sample.pdf`. For changing the
output name, simply append it to the end of the line; e.g. run
```sh
pdfconfig sample.yaml path/to/output.pdf
```

In Windows, use `pdfconfig.exe`.

For more explanation, run
```sh
pdfconfig -- --help 
```
