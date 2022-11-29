---
title: "Livewire Nice Select"
date: 2020-12-30T03:30:44+05:00
draft: true
---

Nice Select is a lightweight jQuery plugin that replaces native select elements with customizable dropdowns. I wanted to make a reactive dropdown with livewire and using the nice select plugin. But it wasn't working as expected. Livewire was not able to bind the `<select>` to a property.

This happened because nice select is a jQuery plugin and emits jQuery events, and not native DOM events. Livewire normally listens for a native change event when wire:model is attached to an element.

To make this work what I end up doing was to register an event listener manually. It was done like so:

```html
 <div wire:ignore\>
<select class\="nice-select"\>
  <option data-display\="Select"\>Nothing</option\>
  <option value\="1"\>Some option</option\>
  <option value\="2"\>Another option</option\>
  <option value\="3" disabled\>A disabled option</option\>
  <option value\="4"\>Potato</option\>
</select\>

</div\>
    
@section('js')
<script\>
    $(document).ready(function() {
        $('.nice-select').niceSelect();
        $('.nice-select').on('change', function (e) {
            @this.set('selectedModel', e.target.value);
        });
    });
</script\>
@endsection
```

So right now everytime the select element changes livewire will recieve the event and set the `$selectedModel` property to the value of the select element. And also to tell Livewire to skip the DOM-diffing for certain parts of the DOM I used wire:ignore directive on the parent `div`.