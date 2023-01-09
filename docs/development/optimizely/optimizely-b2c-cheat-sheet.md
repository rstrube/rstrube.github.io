---
title: 'Optimizely B2C Commerce Cheat Sheet'
tags: ['optimizely', 'cheat sheet']
date: 2023-01-09
---
# Optimizely B2C Commerce Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-09</small>
{: .primaryDate }

---

## Convert to/from Catalog Codes and Content Links

The `ReferenceConverter` class (no interface) can be used convert between a catalog code and a `ContentLink` and vice-versa.

!!! example "Using ReferenceConverter to convert to/from catalog codes and content links"

    ```csharp
    public class MyService
    {
        public void MyTestMethod(string code)
        {
            // get content link from code
            var contentLink = _referenceConverter.GetContentLink(code);

            // do something with the content link
            if(_contentLoader.TryGet<VariationContent>(contentLink, VariationContent variationContent))
            {
                // ...
            }
        }

        public void MyTestMethod2(ContentLink contentLink)
        {
            if(ContentReference.IsNullOrEmpty(contentLink))
                return;

            // get code from content link
            var code = _referenceConverter.GetCode(contentLink);

            // do something with the code
            // ...
        }

        private readonly IContentLoader _contentLoader;
        private readonly ReferenceConverter _referenceConverter;

        // inject services using ioc into class constructor
        public MyService(IContentLoader contentLoader, ReferenceConverter referenceConverter)
        {
            _contentLoader = contentLoader;
            _referenceConverter = referenceConverter;
        }
    }
    ```
