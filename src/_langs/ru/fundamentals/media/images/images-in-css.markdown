---
layout: article
title: "Изображения в CSS"
description: "Группа свойств CSS background позволяет добавлять в элементы комплексные изображения, облегчает добавление сразу нескольких изображений, обеспечивает их повторяемость их и т. д."
introduction: "Группа свойств CSS background позволяет добавлять в элементы комплексные изображения, облегчает добавление сразу нескольких изображений, обеспечивает их повторяемость их и т. д.  В сочетании с медиазапросами background обеспечивает условную загрузку изображений с учетом разрешения экрана, размеров области просмотра и т. д."
authors:
  - petelepage
article:
  written_on: 2014-04-30
  updated_on: 2014-04-30
  order: 2
collection: images
key-takeaways:
  use-right-image:
    - Используйте изображение, которое оптимально подходит под парамеры устройства: размер экрана, разрешение и шаблон страниц.
    - Замените свойство CSS <code>background-image</code> для экранов с высоким разрешением и применением медиазапросов на <code>min-resolution</code> и <code>-webkit-min-device-pixel-ratio</code>.
    - Для изображений в высоком разрешении используйте атрибут srcset (наряду с изображениями 1x в разметке).
    - Учитывайте эффективность затрат при использовании техники замещения текста изображением в JavaScript или сжатии изображений высокого разрешения для устройств с низким разрешением экрана.
  avoid-images:
    - Старайтесь не использовать изображения: применяйте возможности браузера, замещайте изображения символами Unicode, а комплексные значки - иконочными шрифтами.
  optimize-images:
    - Внимательно отнеситесь к формату изображения. Изучите доступные вам форматы и выберите наиболее подходящий.
    - Чтобы уменьшить размер файла, воспользуйтесь инструментами для оптимизации и сжатия изображений.
    - Сократите число HTTP-запросов. Для этого объедините часто запрашиваемые изображения в спрайты.
    - Загрузка изображений должна начинаться, когда они попадают в область просмотра. Так вы ускорите загрузку базовой страницы и уменьшите ее вес.
remember:
  compressive:
    - С осторожностью используйте техники сжатия, так как они занимают больше памяти и требуют затрат на декодирование.  Адаптация крупных изображений для небольших экранов - ресурсоемкий процесс. Он может вызвать проблемы в работе низкопроизводительных устройств с небольшим объемом памяти и ограниченными возможностями обработки данных.
---

{% wrap content%}

<style>
  img, video, object {
    max-width: 100%;
  }

  img.center {
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
</style>

{% include modules/toc.liquid %}

{% include modules/takeaway.liquid list=page.key-takeaways.use-right-image %}

## Используйте медиазапросы для условной загрузки изображений и art direction

Медиазапросы оказывают влияние не только на шаблон страницы, их также можно использовать для условной загрузки изображений и создания эффекта art direction в соответствии с размером области просмотра.

В приведенном ниже примере видно, что на экранах небольшого размера загружен только элемент small.png, он же применен к div. На более крупных экранах свойство background-image: url(body.png) применено к элементу body, а атрибут background-image: url(large.png) применен к div.

{% include_code _code/conditional-mq.html conditional css %}

## Используйте функцию image-set для изображений с высоким разрешением

Функция image-set технологии CSS дополняет группу свойств background: с ее помощью вам будет проще задать несколько изображений для устройств с разными параметрами.  Браузер выберет тот файл, который больше всего подходит для данного устройства с учетом его характеристик. Например, применит 2x изображение для экрана 2x и 1x изображение для экрана 2x, если пропускная способность сети ограничена.

{% highlight css %}
background-image: image-set(
  url(icon1x.jpg) 1x,
  url(icon2x.jpg) 2x
);
{% endhighlight %}

Но это ещё не все. Браузер не только загрузит наиболее подходящее изображение, но и пропорционально изменит его размер
с учетом всех требований. Иными словами, браузер `поймет`, что изображение 2x в два раза тяжелее изображения 1x, и пропорционально уменьшит первое изображение в 2 раза, чтобы сохранить его размер на странице.

Свойство image-set() было введено недавно и поддерживается только в браузерах Chrome и Safari с префиксом -webkit.  Необходимо также предусмотреть резервное изображение на случай, если image-set() не поддерживается. Например:

{% include_code _code/image-set.html imageset css %}

В приведенном выше примере соответствующий цифровой объект будет загружен в браузерах, поддерживающих image-set. В остальных случаях будет использован объект 1x. В настоящее время только немногие браузеры поддерживают image-set(), поэтому, как правило, используются объекты с разрешением 1x.

## Используйте медиазапросы для назначения изображений с высоким разрешением и эффекта art direction

Медиазапросы могут создавать правила на базе [соотношения логических и физических пикселей на устройстве]http://www.html5rocks.com/en/mobile/high-dpi/#toc-bg. Таким образом для экранов 2x и 1x можно применить разные изображения.

{% highlight css %}
@media (min-resolution: 2dppx),
(-webkit-min-device-pixel-ratio: 2)
{
  /* High dpi styles & resources here */
}
{% endhighlight %}

Браузеры Chrome, Firefox и Opera поддерживают стандарт (min-resolution: 2dppx). Для браузеров Safari и Android требуются более старые варианты синтаксиса, а которых отсутствует единица dppx.  Обратите внимание, что загрузка стилей возможна только в том случае, если параметры устройства соответствуют медиазапросу. В остальных случаях вам потребуется также назначить стили.  Таким образом вы сможете задать изображение, которое будет выводиться на экран, если браузер не поддерживает медиазапросы, касающиеся разрешения устройства.

{% include_code _code/media-query-dppx.html mqdppx css %}

Вы также можете использовать синтаксис min-width для отображения альтернативных вариантов изображения в соответствии с размером области просмотра.  Преимущество этой техники в том, что при несоответствующем медиазапросе изображение не загружается.  Например, элемент bg.png загружается и применяется к элементу body, только если ширина браузера 500 или более пикселей:

{% highlight css %}
@media (min-width: 500px) {
  body {
    background-image: url(bg.png);
  }
}
{% endhighlight %}	

{% include modules/nextarticle.liquid %}

{% endwrap %}

