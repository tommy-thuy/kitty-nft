Owning Multiple Kitties
===

Have you noticed a problem with our runtime? What would happen if the same user called the `create_kitty()` function?

Right now our storage can only track one kitty per user, but our total supply of kitties will keep going up! Let's fix that by enabling users to own multiple kitties!

## Using Tuples to Emulate Higher Order Arrays

We are going to need to introduce some more complex storage items to represent ownership of multiple items across multiple users.

Fortunately, for our needs, using a [tuple](https://doc.rust-lang.org/rust-by-example/primitives/tuples.html) of `AccountId` and `Index` can pretty much solve our problems here.

Here is how we could build a "friends list" unique to each person using such a structure:

```rust
MyFriendsArray get(my_friends_array): map (T::AccountId, u32) => T::AccountId;
MyFriendsCount get(my_friends_count): map T::AccountId => u32;
```

This should emulate a more standard two-dimensional array like:

```rust
MyFriendsArray[AccountId][Index] -> AccountId
```

Also we can get the number of friends for a user like:

```rust
MyFriendsArray[AccountId].length()
```

## Relative Index

Just as before, we can optimize the computational work our runtime needs to do by indexing the location of items. The general approach to this would be to reverse the mapping of `MyFriendsArray`, and create a storage item like this:

```rust
MyFriendsIndex: map (T::AccountId, T::AccountId) => u32;
```

Where the `AccountId`s would represent the user, their friend, and the returned value would be the index of `MyFriendsArray` for that user where the friend is stored.

However, since our kitties all have unique identifiers as a `Hash`, and cannot be owned by more than one user, we can actually simplify this structure:

```rust
MyKittiesIndex: map T::Hash => u32;
```

This index tells us for a given kitty, where to look in the *owners* array for that item.

## Your Turn!

There should be nothing too surprising here... just doing some more accounting.

Follow the template to introduce our final set of storage items, and make sure the `create_kitty()` function manages these storage items correctly. It should feel very similar to the previous section.

<!-- tabs:start -->

#### ** Template **

[embedded-code](./assets/2.4-template.rs ':include :type=code embed-template')

#### ** Solution **

[embedded-code-final](./assets/2.4-finished-code.rs ':include :type=code embed-final')

#### ** Previous Chapter Solution **

[embedded-code-previous](./assets/2.3-finished-code.rs ':include :type=code embed-previous')

<!-- tabs:end -->

---
**Learn More**

Talk about Option<T::AccountID>

[TODO: make this a page]

---