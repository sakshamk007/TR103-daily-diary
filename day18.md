# Day 18

## Created wishlist.ejs

```
<div class="bg-gradient-to-b from-lime-700 to-lime-900 lg:w-[400px] w-[350px] lg:h-[500px] h-[400px] rounded-3xl p-4 flex flex-col gap-4 mb-8">
    <div class="text-center lg:text-3xl text-xl">Wishlist</div>
    <ul class="flex flex-col gap-4 overflow-y-auto pb-2">
        <% rows.forEach(row => { %>
            <div class="flex gap-2 w-full justify-center items-center">
                <form action="/wishlist-startbid" method="POST" class="w-full">
                    <input type="hidden" name="wishlist_id" value="<%= row.wishlist_id %>">
                    <button type="submit" class="bg-slate-950 rounded-3xl px-4 py-2 lg:text-lg text-sm text-left w-full transform transition-transform duration-150 ease-in-out active:scale-95"><%= row.title %></button>
                </form>
                <form action="/delete-wishlist" method="POST">
                    <input type="hidden" name="wishlist_id" value="<%= row.wishlist_id %>">
                    <button type="submit" class="bg-red-500 rounded-full p-2 flex justify-center items-center lg:w-[40px] w-[35px] transform transition-transform duration-150 ease-in-out active:scale-95"><img src="/img/delete.svg" alt="" class="w-6"></button>
                </form>
            </div>
        <% }); %>
    </ul>
</div>
```