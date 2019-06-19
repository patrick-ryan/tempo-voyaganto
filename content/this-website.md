Title: This website
Date: 2019-06-09 15:35
Category: Meta

I learned recently that creating and hosting a static website is super easy and cheap. The only thing that cost any money (so far anyway) was the domain name. Apparently Google Cloud Storage allows you to host static files by providing a CNAME record to the google storage service:
## Step 1: buy a domain name
I used [Namecheap](https://www.namecheap.com) since I have another domain from there already
## Step 2: set up Google Storage
<https://cloud.google.com/storage/docs/hosting-static-website>
## Step 3: choose/run a static site generator
<https://www.staticgen.com/>

I chose [Pelican](http://docs.getpelican.com) since it was a highly-rated Python-based generator with Jinja2 templating, it's working out so far.

Pelican also has a nice variety of public themes to choose from:

* <http://www.pelicanthemes.com/>

Here is the run output I got after starting the project and creating an article:
```bash
$ pelican-quickstart
Welcome to pelican-quickstart v4.0.1.

This script will help you create a new Pelican-based website.

Please answer the following questions so this script can generate the files
needed by Pelican.

    
> Where do you want to create your new web site? [.] 
> What will be the title of this web site? Tempo Vojaĝanto
> Who will be the author of this web site? Pat^2
> What will be the default language of this web site? [en] 
> Do you want to specify a URL prefix? e.g., https://example.com   (Y/n) y
> What is your URL prefix? (see above example; no trailing slash) http://www.tempovojaganto.space
> Do you want to enable article pagination? (Y/n) y
> How many articles per page do you want? [10] 
> What is your time zone? [Europe/Paris] US/Chicago
Please enter a valid time zone:
 (check [http://en.wikipedia.org/wiki/List_of_tz_database_time_zones])
> What is your time zone? [Europe/Paris] America/Chicago
> Do you want to generate a tasks.py/Makefile to automate generation and publishing? (Y/n) y
> Do you want to upload your website using FTP? (y/N) n
> Do you want to upload your website using SSH? (y/N) n
> Do you want to upload your website using Dropbox? (y/N) n
> Do you want to upload your website using S3? (y/N) n
> Do you want to upload your website using Rackspace Cloud Files? (y/N) n
> Do you want to upload your website using GitHub Pages? (y/N) n
Done. Your new project is available at /home/patrick/Builds/personal/tempo-vojaganto

$ pelican content
Done: Processed 1 article, 0 drafts, 0 pages, 0 hidden pages and 0 draft pages in 0.14 seconds.

$ pelican --listen
```
To the `pelicanconf.py` file I added this setting to account for favicon.ico and robots.txt files:
```python
STATIC_PATHS = [
    'extra',
]
EXTRA_PATH_METADATA = {
    'extra/robots.txt': {'path': 'robots.txt'},
    'extra/favicon.ico': {'path': 'favicon.ico'},
}
```
Project directory structure:
```bash
$ tree -L 3 .
.
├── content
│   ├── about-the-name.md
│   ├── extra
│   │   ├── favicon.ico
│   │   └── robots.txt
│   └── this-website.md
├── Makefile
├── output
├── pelicanconf.py
├── publishconf.py
├── __pycache__
└── tasks.py
```

## Step 4: add a publish script
Pelican doesn't support a publish script for Google Storage out of the box, but with `invoke` and `gsutil` it's a pretty simple thing:
```python
@task
def publish_to_gstorage(c):
    """Publish to production via google storage upload"""
    c.run(f'gsutil cp -m -r {CONFIG["deploy_path"]}/* gs://{CONFIG["gcloud_bucket_name"]}')
```
## Step 5: profit!
<br>

# Conclusion
Total monetary cost of the website so far is ~$2, for the domain. The complexity of the site is also pretty minimal - not much setup time at all.
