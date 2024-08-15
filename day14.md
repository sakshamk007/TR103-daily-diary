# Day 14

## Created signup and signin views

### signup.ejs
```
<form action="/auth/signup" method="post" class="flex items-center flex-col gap-6">
    <div class="lg:text-4xl text-2xl">Sign up</div>
    <input type="email" name="email" required placeholder="email" class="bg-black rounded-lg p-2 lg:w-[400px] w-[300px] h-12">
    <div class="flex justify-center relative">
        <input type="password" name="password" required placeholder="Create password" class="bg-black rounded-lg p-2 lg:w-[400px] w-[300px] h-12 password">
        <img src="/img/invisible.svg" alt="" class="visibility absolute right-4 top-4">
    </div>
    <div class="flex justify-center relative">
        <input type="password" name="confirmPassword" required placeholder="Confirm password" class="bg-black rounded-lg p-2 lg:w-[400px] w-[300px] h-12 password">
        <img src="/img/invisible.svg" alt="" class="visibility absolute right-4 top-4">
    </div>
    <button type="submit" class="lg:w-[400px] w-[300px] bg-violet-800 p-2 rounded-lg h-12 lg:text-xl text-lg transform transition-transform duration-150 ease-in-out active:scale-95">Sign up</button>
    <h1>Already have an account? <a href="/auth/signin" class="text-violet-400">Sign in</a></h1>
</form>
```

### signin.ejs
```
<form action="/auth/signin" method="post" class="flex items-center flex-col gap-6">
    <div class="lg:text-4xl text-2xl">Sign in</div>
    <input type="email" name="email" required placeholder="email" class="bg-black rounded-lg p-2 lg:w-[400px] w-[300px] h-12">
    <div class="flex justify-center relative">
        <input type="password" name="password" required placeholder="password" class="bg-black rounded-lg p-2 lg:w-[400px] w-[300px] h-12 password">
        <img src="/img/invisible.svg" alt="" class="visibility absolute right-4 top-4">
    </div>
    <button type="submit" class="lg:w-[400px] w-[300px] bg-violet-800 p-2 rounded-lg h-12 lg:text-xl text-lg transform transition-transform duration-150 ease-in-out active:scale-95">Sign in</button>
    <h1>Don't have an account? <a href="/auth/signup" class="text-violet-400">Sign up</a></h1>
</form>
```