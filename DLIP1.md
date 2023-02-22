# Departure Labs DLIP 1 - Interface Support

```
version : v0
interface_identifier : DL::DLIP_1
```

## References
- ERC-165 [https://eips.ethereum.org/EIPS/eip-165](https://eips.ethereum.org/EIPS/eip-165)

## Summary 
This standard defines a canister method to detect and publish the interfaces implemented by a canister.

## Rationale

The `dl_supports_interface_v0` method allows canisters to declare which interfaces they implement. This is useful for other canisters to detect and interact with canisters that support specific interfaces.

By defining a standardized format for interface identifiers, conflicts can be avoided between different standards that use similar names or abbreviations.

## Specification

The canister **must** implement the following Candid interface:

```candid
service : {
    dl_supports_interface_v0 :(text) -> (bool)
}
```

To avoid conflicts, implementers **must** publish interface identifiers in the following format:

```
string_concat("{}::{}", GROUP, STANDARD_IDENTIFIER)
```

Here, `GROUP` refers to the group or organization that published the standard and `STANDARD_IDENTIFIER` is a unique identifier for the standard. For example, the `DL::ICRC_1` identifier is used for this Departure Labs DLIP 1 standard.






















