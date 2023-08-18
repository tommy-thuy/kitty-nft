Storing a Structure
===

If you thought everyone getting their own number was cool, lets try to give them all digital kitties!

First we need to define what properties our kitties have in the form of a `struct`, and then we need to learn how to store these custom `structs` in our runtime storage.

## Defining a Custom Struct

You can define a custom struct for your runtime like so:

```rust
#[derive(Encode, Decode, Default, Clone, PartialEq)]
#[cfg_attr(feature = "std", derive(Debug))]
pub struct MyStruct<A, B> {
    some_number: u32,
    some_generic: A,
    some_other_generic: B,
}
```

To use the custom `Encode` and `Decode` traits, you will need to import them from the `parity_codec` crate:

```rust
use parity_codec::{Encode, Decode};
```

This should look pretty normal compared to defining structs in other languages. However, you will notice two oddities about this declaration for runtime development...

### Using Generics

You will notice that we define our example struct using a generic as one of the types that we store. This will be important when trying to use custom Substrate types like `AccountId` or `Balance` within our struct as we will need to pass in these types every time we use our struct.

So if we wanted to store a `Balance` in `some_generic` and `Hash` in `some_other_generic`, we would need to define our storage item like this:

```rust
decl_storage! {
    trait Store for Module<T: Trait> as Example {
        MyItem: map T::AccountId => MyStruct<T::Balance, T::Hash>;
    }
}
```

For the purposes of clarity, we will name a generic type for `T::AccountId` as `AccountId` or `T::Balance` as `Balance`. You can use comma separate and add more generics as needed following this pattern.

### Derive Macro

The other thing you will notice is `#[derive(...)]` at the top. This is an attribute provided by the Rust compiler which allows basic implementations of some traits. The second line, `#[cfg_attr(feature = "std", derive(Debug))]` does the same thing for the `Debug` trait, but only when using the "standard" libraries, i.e. when compiling the native binaries and not the Wasm. You can learn more about that [here](https://doc.rust-lang.org/rust-by-example/trait/derive.html). For the purposes of this tutorial you can treat it like magic.

## Custom Struct in Module Function

Now that we have initialized our custom struct in our runtime storage, we can now push values and modify it.

Here is an example of creating and inserting a struct into storage using a module function:

```rust
decl_module! {
    pub struct Module<T: Trait> for enum Call where origin: T::Origin {
        fn create_struct(origin, value: u32, balance: T::Balance, hash: T::Hash) -> Result {
            let sender = ensure_signed(origin)?;

            let new_struct = MyStruct {
                some_number: value,
                some_generic: balance,
                some_other_generic: hash,
            };

            <MyItem<T>>::insert(sender, new_struct);
            Ok(())
        }
    }
}
```

## Your Turn!

Update your storage mapping runtime to store a `Kitty` struct instead of a u64.

A `Kitty` should have the following properties:

 - `id` : `Hash`
 - `dna` : `Hash`
 - `price` : `Balance`
 - `gen` : `u64`

We have created a skeleton of the `create_kitty()` function for you, but you will need to add the logic. Include code to create a `new_kitty` using the `Kitty` object and store that object into your runtime storage.

To initialize your `T::Hash` and `T::Balance` you can use:

```rust
let hash_of_zero = <T as system::Trait>::Hashing::hash_of(&0);

let my_zero_balance = <T::Balance as As<u64>>::sa(0);
```

We will also have `gen` start as `0`.

<!-- tabs:start -->

#### ** Template **

[embedded-code](./assets/1.6-template.rs ':include :type=code embed-template')

#### ** Solution **

[embedded-code-final](./assets/1.6-finished-code.rs ':include :type=code embed-final')

#### ** Previous Chapter Solution **

[embedded-code-previous](./assets/1.5-finished-code.rs ':include :type=code embed-previous')

<!-- tabs:end -->

---
**Learn More**

### Strings in Substrate

 You might expect that one of the properties we add for our kitties is a name! After all, who doesn't name the things they love?

Substrate does not directly support `Strings`. Runtime storage is there to store the state of the business logic on which the runtime operates. It is not to store general data that the UI needs. If you really need to store some arbitrary data into your runtime, you can always create a bytearray (`Vec<u8>`), however the more logical thing to do is to store a hash to a service like IPFS to then use to fetch data for your UI. This is currently beyond the scope of this workshop, but may be added later to support additional metadata about your kitty.

---
