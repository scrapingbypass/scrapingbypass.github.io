---
title: How does Python Selenium bypass cloudflare to crawl web pages?
categories:
- Cloudflare
- Python
- Selenium
excerpt: |
  How does Python Selenium bypass cloudflare to crawl web pages?
feature_text: |
  ## How does Python Selenium bypass cloudflare to crawl web pages?
  
feature_image: "https://picsum.photos/2560/600?image=872"
---
  
  

Like many websites, Cloudflare also detects access to see if it is initiated by a Selenium bot. This detection mainly focuses on whether there are unique js variables, such as variables containing "selenium" and "webdriver", or file variables containing "$cdc_" and "$wdc_".  

The detection mechanism of each driver may be different, the following solutions are mainly for chromedriver.  

1. Use Undetected-chromedriver This is a very convenient package that can be installed directly through pip. Then initialize the driver like below, after that it works just like regular Selenium usage.  
   
```
python Copy code

import undetected_chromedriver as uc
driver = uc.Chrome()
driver.get('https://nowsecure.nl')
```

2. Directly modify the chromedriver executable file You can change the key variable to any character that does not contain "cdc".  
   
```
javascript Copy code

/**
 * Returns the global object cache for the page.
 * @param {Document=} opt_doc The document whose cache to retrieve. Defaults to
 *     the current document.
 * @return {!Cache} The page's object cache.
 */
function getPageCache(opt_doc, opt_w3c) {
  var doc = opt_doc || document;
  var w3c = opt_w3c || false;
  // |key| is a long random string, unlikely to conflict with anything else.
  var key = '$cdc_asdjflasutopfhvcZLmcfl_';
  if (w3c) {
    if (!(key in doc))
      doc[key] = new CacheWithUUID();
    return doc[key];
  } else {
    if (!(key in doc))
      doc[key] = new Cache();
    return doc[key];
  }
}
```

There is not much difference between these two methods in essence. In fact, undetected-chromedriver will apply a patch when starting chromedriver, completing the steps of modifying the key. 

```
python Copy code

def patch_exe(self):
    """
    Patches the ChromeDriver binary
    :return: False on failure, binary name on success
    """
    logger.info("patching driver executable %s" % self.executable_path)
 
    linect = 0
    replacement = self.gen_random_cdc() #这里修改了cdc的名称
    with io.open(self.executable_path, "r+b") as fh:
        for line in iter(lambda: fh.readline(), b""):
            if b"cdc_" in line:
                fh.seek(-len(line), 1)
                newline = re.sub(b"cdc_.{22}", replacement, line)
                fh.write(newline)
                linect += 1 
```

3. Using the **ScrapingBypass** API, you can easily [bypass Cloudflare](https://www.scrapingbypass.com) robot verification, even if you need to send 100,000 requests, you don't have to worry about being identified as a scraper.  
A ScrapingBypass API can break through all anti-anti-bot robot inspections, easily bypass Cloudflare, CAPTCHA verification, WAF, CC protection, and provide HTTP API and Proxy, including interface address, request parameters, return processing; and set Referer, browser UA and headless status and other browser fingerprint device features.
