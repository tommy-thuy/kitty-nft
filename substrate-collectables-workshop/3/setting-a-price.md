Set the Price of a Kitty
===

The core of our collectables chain is done! Users can now create and track ownership of their Substratekitties.

But if we want to make our runtime more like a game, we will need to introduce other functions like buying and selling. We will start those features by first enabling users to update the price of their kitty.

## Updating a Stored Struct

Every `Kitty` object has a `price` attribute that we have set to 0 as default. If we want to update the price of a kitty, we will need to pull down the `Kitty` object, update the price, and push it back into storage.

Remember that Rust expects you to declare a variable as mutable (`mut`) if the value is going to be updated, so we should do that here:

```rust
let mut object = Self::get_object(object_id);
object.value = new_value;

<Objects<T>>::insert(object_id, object);
```

## Permissioned Functions

Any user can call our `create_kitty()` function with a signed message, but as we create functions which modify objects, we should check that only the appropriate users are successful in making those calls.

For modifying a `Kitty` object, we will need to get the `owner` of kitty, and `ensure` that it is the same as the `sender`.

`KittyOwner` stores a mapping to an `Option<T::AccountId>` since a given `Hash` may not point to a generated and owned `Kitty` yet. This means, whenever we fetch the `owner` of a kitty, we need to resolve the possibility that it returns `None`. This could be caused by bad user input or even some sort of problem with our runtime, but checking will help prevent these kinds of problems.

An ownership check for our module will look something like this:

```rust
let owner = Self::owner_of(object_id).ok_or("No owner for this object")?;

ensure!(owner == sender, "You are not the owner");
```

## Sanity Checks

We are going to start letting users call public functions that our runtime exposes, and that means opportunity for our users to give poor input unintentionally or even maliciously.

We need to ensure that our runtime is consistently doing sanity checks so that we do not allow users to break our chain. If we are creating a function which updates the value of an object, the first thing we better do is make sure the object exists at all.

```rust
ensure!(<MyObject<T>>::exists(object_id));
```

## Your Turn!

You now have all the tools necessary to update your `Kitty` object. Remember, **"verify first, write last"**. Make sure you are performing all the appropriate checks before you actually change storage, and do not assume that the user is giving you good data to work with!

Follow the template in this section to help you create the `set_price()` function.

<!-- tabs:start -->

#### ** Template **

[embedded-code](./assets/3.1-template.rs ':include :type=code embed-template')

#### ** Solution **

[embedded-code-final](./assets/3.1-finished-code.rs ':include :type=code embed-final')

#### ** Previous Chapter Solution **

[embedded-code-previous](../2/assets/2.6-finished-code.rs ':include :type=code embed-previous')

<!-- tabs:end -->