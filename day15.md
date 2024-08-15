# Day 15

## Created success and error views

### success.ejs
```
<div class="flex items-center flex-col lg:gap-16 gap-8">
    <div class="flex lg:gap-8 gap-4 justify-center items-center">
        <img src="/img/success.svg" alt="" class="lg:w-[80px] w-[60px]">
        <div class="lg:text-5xl text-4xl">Success <%= status %></div>
    </div>
    <div class="lg:text-4xl text-2xl text-center"><%= message %></div>
    <div class="lg:text-2xl text-xl">Click here to <a href="javascript:history.back()" class="text-violet-400">Go back</a></div>
</div>
```

### error.ejs
```
<div class="flex items-center flex-col lg:gap-16 gap-8">
    <div class="flex lg:gap-8 gap-4 justify-center items-center">
        <img src="/img/error.svg" alt="" class="lg:w-[80px] w-[60px]">
        <div class="lg:text-5xl text-4xl">Error <%= status %></div>
    </div>
    <div class="lg:text-4xl text-2xl text-center"><%= message %></div>
    <div class="lg:text-2xl text-xl">Click here to <a href="javascript:history.back()" class="text-violet-400">Go back</a></div>
</div>
```