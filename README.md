# hotpdf

## Hot PDF

A faster alternative to libraries like pdfquery.
[Read the docs](https://stdocs.z6.web.core.windows.net/hotpdf/index.html)

### Pre-requisites

1. **Ghostscript Installation:**
   hotpdf requires Ghostscript to be installed. Please install it on your system.
   
   [Download Ghostscript](https://www.ghostscript.com/).

### Contributing

1. **Type & Lint Checks:**
   - Use `mypy` to check for types and maintain code quality.
   - Run mypy locally before pushing to prevent lint pipeline failures.
   - hotpdf/ folder should have **strict** mypy type checking
   - Use `flake8` for basic linting.

    ```bash
    pipenv run mypy hotpdf/ --check-untyped-defs --strict
    pipenv run mypy tests/ --check-untyped-defs
    pipenv run flake8 hotpdf/ --ignore=E501,W503
    ```

2. **Test Coverage:**
   - Check test coverage with the `coverage` library.
   - Aim for 100% test coverage.

    ```bash
    pipenv run coverage run --omit="*/test*" -m pytest tests/
    pipenv run coverage report -m --fail-under=100 -m
    ```


## Comprehensive Documentation
`hotpdf` is comprehensively documented within code for easy understanding. You can use any tool to view the documentation. 
You can use an inbuilt tool like `pydoc` to view the documentation.

However we can also generate a better looking documentation using tools like [pdoc3](https://pypi.org/project/pdoc3/).

### View documentation
```
pipenv run python -m pdoc --http localhost:8081 hotpdf/
```
This will start the documentation server on `localhost:8081`

[View documentation](https://stdocs.z6.web.core.windows.net/hotpdf/index.html)

---


## Usage
(Please refer to aforementioned docs for more granular documentation)

First, import the library

```python
# Import
from hotpdf import HotPdf
```

### Loading

Initialising a HotPdf object

```python
hotpdf = HotPdf()

# Load the file into memory
hot_pdf.load("test.pdf")
```

### Operations

#### load(str)

Loads the file in memory.
Params

- file_name (str): Path of the file to load
- drop_duplicate_spans (bool) (Optional): Drop duplicate text spans while loading in memroy (default: True)
- first_page (int) (Optional): Page to start loading from. (Default: 0) If nothing is specified, whole file is loaded
- last_page (int) (Optional): Last page to be loaded. (Default: 0) If nothing is specified, whole file is loaded.

#### find_text (string)

Returns the occurences where the string was found in page wise. (dict[list])

Params:

- query (str): The text that you are trying to search for
- pages (list) (Optional): List of pages you want to search in
- validate (bool) (Optional): Double check if the text extracted is the text you want to fetch
- take_span (bool) (Optional): Extract the whole span that the text is a child of.

```python
# Find occurences of word in the pdf
occurences = hot_pdf.find_text("Auszahlungsbetrag")
"""
Example
{
    page_num: [
        [
            {co-ordinates of char1}, {co-ordinates of char2}, .....
        ]
    ]
}
"""
```

To find the total span of the word, take the 'x' & y value of the first character and the 'x_end' & 'y_end' values of the last character

#### extract_text(x0, y0, x1, y1, page)

Returns the text in given bbox span (str)

```python
# Extract text from bbox
hot_pdf.extract_text(x0=513, y0=760, x1=560, y1=766, page=0)
```

#### extract_spans(x0, y0, x1, y1, page)

Returns the spans that lie within the specified coordinates. Returns a list of spans.

```python
# Extract text from bbox
hot_pdf.extract_spans(x0=513, y0=760, x1=560, y1=766, page=0)
```

### Anatomy

#### MemoryMap

A memory map is the internal 2D matrix representation of a PDF page. The x values and y values in the matrix are positioned according to the bbox position.

#### page (MemoryMap)

You can access a page by accessing the hotpdf.pages object

```python
pages = hot_pdf.pages[0]

# Height and Width
pages[0].height # number of rows
pages[0].width # number of columns

# Number of pages
num_pages = len(pages)

# View the text
pages[0].text()

# Save the text to a file to debug
pages[0].display_memory_map(save=True, filename="output.txt")
