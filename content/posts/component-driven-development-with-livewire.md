---
title: "Component Driven Development With Livewire"
date: 2020-11-30T03:23:17+05:00
draft: false
---

For the past few days, I have been working with livewire for my projects and so far I enjoy how easy it is and it takes away the complexity of actually using Javascript to request for ajax and having to manually set up the tracking of states within the page. And all the action inside I handled with in the component. So there won't be any need for you to have a controller.

### So what is Livewire?
Livewire is a full-stack framework for Laravel that makes building dynamic interfaces simple, without leaving the comfort of Laravel.

Lets me show you a simple data table component I built with livewire for my project.

`OrderTable.php`

```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;
use Livewire\WithPagination;

class OrderTable extends Component
{
    use WithPagination;

    public $perPage = 10;
    public $sortField = 'customer_name';
    public $sortAsc = true;
    public $search = '';


    public function sortBy($field)
    {
        if ($this->sortField === $field) {
            $this->sortAsc = ! $this->sortAsc;
        }else {
            $this->sortAsc = true;
        }
        $this->sortField = $field;
    }

    public function render()
    {
        return view('livewire.order-table', [
            'orders' => \App\Order::search($this->search)
                ->orderBy($this->sortField, $this->sortAsc ? 'asc' : 'desc')
                ->paginate($this->perPage),
        ]);
    }
}
```
This component is responsible for listing all the orders in the application and to have sorting and also paginations. Livewire makes it super easy to handle these kinda things. All the public properties state that will be passed to the component view.

`order-table.blade.php`

```html
<div class="card mb-4">
    <div class="card-header"><i class="fas fa-table mr-1"></i>Orders</div>
    <div class="card-body">

        <div class="row mb-4">
            <div class="col form-inline">
                Per Page: &nbsp;
                <select wire:model="perPage" class="form-control">
                    <option>10</option>
                    <option>15</option>
                    <option>25</option>
    
                </select>
            </div>
            <div class="col">
                <input wire:model="search" class="form-control" type="text" placeholder="Search" >
            </div>
    
        </div>
    
        <div class="table-responsive">
            <table class="table table-bordered" width="100%" cellspacing="0">
                <thead>
                    <tr>
                        <th>Customer Name</th>
                        <th>Customer Phone</th>
                        <th>Customer Address</th>
                        <th>Delivery Status</th>
                        <th>Created at</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach ($orders as $order)
                    <tr>
                        <td>{{$order->customer_name}}</td>
                        <td>{{$order->customer_phone}}</td>
                        <td>{{$order->customer_address}}</td>
                        <td>
                            @if($order->delivered == 0)
                            <span class="badge badge-primary">Not delivered</span>
                            @else
                            <span class="badge badge-success">Delivered</span>
                            @endif
                        </td>
                        <td>{{$order->created_at}}</td>
                    </tr>
                    @endforeach
                </tbody>
            </table>
        </div>

        <div class="row">
            <div class="col">
                {{$orders->links()}}
            </div>
            <div class="col text-right text-muted">
                Showing {{$orders->firstItem()}} to {{$orders->lastItem()}} out of {{$orders->total()}} results
            </div>
        </div>
    </div>
</div>
</div>
```
The idea of calling and accessing the methods and properties stored in the backend with with front-end code really blew my mind.

Just like data binding in Vue. You can bind the property to an HTML element by using `wire:model="{property}"`