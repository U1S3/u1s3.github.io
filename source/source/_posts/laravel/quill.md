---
title: Laravel 使用 Quill 富文本编辑器
date: 2019-11-1 15:15:26
categories: Laravel
---

## 安装

```bash
$ yarn add quill -D
```

*webpacl.mix.js*

```php
mix.copy('node_modules/quill/dist/quill.snow.css', 'public/css')
  .copy('node_modules/quill/dist/quill.js', 'public/js');
```

## 模板调用

*x.blade.php*

```php
<div id="toolbar>
    <span class="ql-formats">
        <select class="ql-size"></select>
        <button class="ql-header" value="1"></button>
        <button class="ql-header" value="2"></button>
        <button class="ql-bold"></button>
        <button class="ql-italic"></button>
        <button class="ql-underline"></button>
        <button class="ql-strike"></button>
        <select class="ql-align"></select>
        <select class="ql-color"></select>
        <select class="ql-background"></select>
        <button class="ql-blockquote"></button>
        <button class="ql-code-block"></button>
    </span>
    <span class="ql-formats">
        <button class="ql-list" value="ordered"></button>
        <button class="ql-list" value="bullet"></button>
        <button class="ql-indent" value="-1"></button>
        <button class="ql-indent" value="+1"></button>
    </span>
    <span class="ql-formats">
        <button class="ql-link"></button>
        <button class="ql-image"></button>
        <button class="ql-video"></button>
    </span>
    <span class="ql-formats">
        <button class="ql-clean"></button>
    </span>
</div>
<div id="editor"></div>
<input type="hidden" id="body" name="body">

{{-- Quill --}}
@push('styles')
  <link rel="stylesheet" href="{{ mix('css/quill.snow.css') }}">
@endpush
@push('scripts')
  <script src="{{ mix('js/quill.js') }}"></script>
  <script>
      var quill = new Quill('#editor', {
          theme: 'snow',
          modules: {
              toolbar: '#toolbar'
          },
      });
      // 监视文本变动 自动赋值到 input
      quill.on('text-change', function(delta, oldDelta, source) {
          $("input[name=body]").val(quill.container.firstChild.innerHTML);
      });
  </script>
@endpush
```

