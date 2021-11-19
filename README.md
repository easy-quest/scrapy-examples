scrapy-examples
==============

Многофункциональные сцепные примеры со встроенным прокси и агентами, которые позволяют вам удобно написать паук.

Не используйте это, чтобы сделать что-то незаконное!

***
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#git@github.com:easy-quest/scrapy-examples.git)

## Реальный пример паука: Dourbanbook

#### Руководство

```shell
git clone git@github.com:easy-quest/scrapy-examples.git
```
```shell
cd scrapy-examples/doubanbook
```
```console
scrapy crawl doubanbook
```

#### Depth

Есть несколько глубин в пауке, а паук получает
реальные данные из depth2.

- Depth0: The entrance is `http://book.douban.com/tag/`
- Depth1: Urls like `http://book.douban.com/tag/外国文学` from depth0
- Depth2: Urls like `http://book.douban.com/subject/1770782/` from depth1

#### Пример изображения
![douban book](https://raw.githubusercontent.com/geekan/scrapy-examples/master/doubanbook/sample.jpg)

***

## Доступные пауки

* tutorial
  * dmoz_item
  * douban_book
  * page_recorder
  * douban_tag_book
* doubanbook
* linkedin
* hrtencent
* sis
* zhihu
* alexa
  * alexa
  * alexa.cn

## Передовой

* Используйте `parse_with_Rules`, чтобы писать паук быстро. 
  Смотрите Dmoz Spider для более подробной информации.

* Proxies
  * If you don't want to use proxy, just comment the proxy middleware in settings.  
  * If you want to custom it, hack `misc/proxy.py` by yourself.  

* Notice
  * Don't use `parse` as your method name, it's an inner method of CrawlSpider.

### Расширенное использование

* Запустить `./startproject.sh <Project>` Чтобы начать новый проект.  
  Он автоматически генерирует большинство вещей, только левые вещи:
  * `PROJECT/PROJECT/items.py`
  * `PROJECT/PROJECT/spider/spider.py`

#### Пример взломал `items.py` а также `spider.py`

Взломанные `items.py` с дополнительными полями` URL` и`description`:  
```python
from scrapy.item import Item, Field

class exampleItem(Item):
    url = Field()
    name = Field()
    description = Field()
```

Взломал `spider.py` с началаьными правилами и правилами CSS (здесь только отображать) class exampleSpider):  

```python
class exampleSpider(CommonSpider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.com/",
    ]
    # Shrawler начнется на Start_urls, и следуйте действительным URL-адресам, разрешенным ниже правила.
    rules = [
        Rule(sle(allow=["/Arts/", "/Games/"]), callback='parse', follow=True),
    ]

    css_rules = {
        '.directory-url li': {
            '__use': 'dump', # непосредственно давить данные
            '__list': True, # Это список
            'url': 'li > a::attr(href)',
            'name': 'a::text',
            'description': 'li::text',
        }
    }

    def parse(self, response):
        info('Parse '+response.url)
        # Parse_with_Rules реализован здесь:
        #   https://github.com/geekan/scrapy-examples/blob/master/misc/spider.py
        self.parse_with_rules(response, self.css_rules, exampleItem)
```

