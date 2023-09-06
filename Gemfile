source "https://rubygems.org"

# Версия jekyll
gem "jekyll", "~> 4.3.2"

# Плагины
group :jekyll_plugins do
  # RSS
  # https://github.com/jekyll/jekyll-feed
  gem "jekyll-feed", "~> 0.12"

  # SEO
  # https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/installation.md
  # gem install jekyll-seo-tag
  gem "jekyll-seo-tag"

  # Hightlight
  # https://jun711.github.io/web/how-to-highlight-code-on-a-Jekyll-site-syntax-highlighting/
  # https://bnhr.xyz/2017/03/25/add-syntax-highlighting-to-your-jekyll-site-with-rouge.html
  # rougify help style
  # rougify style monokai > _sass/scaffolds/components/_syntax.scss
  # gem install kramdown rouge
  gem "kramdown"
  gem "rouge"

  # Пагинация
  # https://github.com/sverrirs/jekyll-paginate-v2
  # gem install jekyll-paginate-v2
  gem "jekyll-paginate-v2"

  # Добавляет подкоманды для создания записи, страницы или черновика.
  # https://github.com/jekyll/jekyll-compose
  # gem install jekyll-compose
  gem "jekyll-compose"

  # Добавляет site map
  # https://github.com/jekyll/jekyll-sitemap
  # gem install jekyll-sitemap
  gem "jekyll-sitemap"

  # Архивы
  # https://github.com/jekyll/jekyll-archives
  # gem install jekyll-archives
  gem "jekyll-archives"

  # SVG
  # https://github.com/sdumetz/jekyll-inline-svg
  # gem install jekyll-inline-svg
  gem "jekyll-inline-svg"

  # Responsive image
  # Меняет размер изображения
  # https://github.com/wildlyinaccurate/jekyll-responsive-image
  # sudo apt-get install imagemagick libmagickcore-dev libmagickwand-dev
  # gem install jekyll-responsive-image
  # gem "jekyll-responsive-image"

  # Автоматически генерирует webp изображения
  # https://github.com/sverrirs/jekyll-webp
  # gem install jekyll-webp
  gem "jekyll-webp"

  # Меняет размер изображения
  # https://github.com/generalui/jekyll-image-size
  # gem install jekyll-image-size
  gem "jekyll-image-size"

  # Минификация js
  # https://github.com/digitalsparky/jekyll-minifier
  # gem install jekyll-minifier
  # gem "jekyll-minifier"

  # Resize изображения
  # https://github.com/MichaelCurrin/jekyll-resize
  # https://github.com/MichaelCurrin/jekyll-resize/blob/master/docs/usage.md
  # gem "jekyll-resize", git: "https://github.com/MichaelCurrin/jekyll-resize"

  # Обрезка и изменение размера изображений
  # https://github.com/zroger/jekyll-minimagick
  # https://github.com/MattKevan/Jekyll-MiniMagick-new
  # https://www.kevan.tv/2016/10/17/automatic-image-resizing-with-jekyll-and-imagemagick/
  # gem "jekyll-minimagick"
  # gem "jekyll-minimagick", github: "benubois/jekyll-minimagick", branch: "patch-1"
  gem "mini_magick"

  # Предоставляет механизм для передачи содержимого со страниц в их родительские макеты.
  # https://github.com/rustygeldmacher/jekyll-contentblocks
  # gem install jekyll-contentblocks
  #gem 'jekyll-contentblocks', path: './_plugins/jekyll-contentblocks-1.2.0'
  gem 'jekyll-contentblocks'
end

# Добавление расширений, которые предоставляют информацию и возможность работы с часовыми поясами (timezone).
# Они полезны, когда вам нужно работать с датами и временем в разных часовых поясах в Ruby-приложениях.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

# Ускоритель производительности для просмотра каталогов в Windows
gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

# Заблокируйте gem `http_parser.rb` на `v0.6.x` в сборках JRuby, поскольку более новые версии гема не имеют аналога Java
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]

# Для компиляции проекта через Makefile
# https://github.com/rails/execjs
gem "execjs"
gem "therubyracer", :platforms => :ruby
