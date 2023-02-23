# Departure Labs DLIP 2 - Simple Canister Events

```
version : v0
interface_identifier : DL::DLIP_2v0
supports : DL::DLIP_1
authors : Hazel Rowell,
```

## Summary
This is an interface standard that facilitates publishing and consuming canister events.

## Rationale
Canister events have the potential to level up the IC ecosystem by enabling developers to build reactive building blocks more efficiently. By publishing and consuming events, canisters can communicate with each other and trigger actions in response to events. This can be particularly useful in building real-time applications that require instant updates based on changes in data or user interactions. With this interface standard, we hope to provide a minimal set of features that will enable developers to easily implement the publish-subscribe pattern to start leveraging the of the power of canister events.

## Goals 
The goal of this interface is to provide a minimal set of features to implement the most basic usable version of the publish-subscribe pattern.

## Types

### `event`

A `record` that represents a canister event, containing the following fields:

- `creator` (optional): a principal that represents the identity of the user that created the event.
- `source_method`: a text that represents the name of the canister method that triggered the event.
- `created_at`: a nat64 that represents the timestamp of when the event was created.
- `data`: a vec of nat8 that represents the bytes data of the event.

### `subscribe_request`
A `record` that represents a request to subscribe to a canister event, containing the following fields:

- `method_name`: a text that represents the name of the canister method that will be called when the event is triggered.
- `canister`: a principal that represents the identity of the canister that will handle the event.

### `subscription_result`
A `variant` that represents the result of a subscription request, containing either:

- `Ok`: a unit value that indicates a successful subscription.
- `Err`: a record that contains an error message.

### `canister_event_information`
A `record` that represents information about a canister event, containing the following field:

- `event_schema_link`: a text that points to the schema of the event.

## Service Specification

The service defines the following methods:

### `dl_subscribe_simple`
A method that takes a subscribe_request and returns a subscription_result. This method is used to subscribe to a canister event.

```candid
service {
  dl_subscribe_simple: (subscribe_request) -> (subscription_result)
}
```

### `dl_remove_simple_subscription`
A method that takes a `subscribe_request`. This method is used to unsubscribe from a canister event.

```candid
service : {
  dl_remove_simple_subscription: (subscribe_request) -> ()
}
```

### `dl_canister_events_information`
A method that takes no arguments and returns a canister_event_information. This method is used to get information about the canister

## Integrators
### Subscribing to Events

To receive events, an integrator needs to follow the following steps:

Subscribe to the canister event using the `dl_subscribe_simple` method by providing a `subscribe_request` that includes the name of the canister method that will be called when the event is triggered and the identity of the canister that will handle the event.

Handle the event in the canister method specified in the subscribe_request. The method must conform to `func (vec event) -> ()`, where event is a record representing the canister event. For example:

```candid
type event = record {
  creator : opt principal;
  source_method : text;
  created_at : nat64;
  data : vec nat8;
};

service : {
  my_callback : (vec event) -> ();
}
```

### Unsubscribing to Events
Unsubscribe from the canister events using the `dl_remove_simple_subscription` method when the subscription is no longer needed.

### Getting Schema Information
To get information about the canister, the integrator can call the `dl_canister_events_information` method, which returns a `canister_event_information` record that includes a link to a schema describing the events of the canister. Please refer to the link for more specific details about how to interpret the payload



## Candid Interface

```candid
type event = record {
  creator : opt principal;
  source_method : text;
  created_at : nat64;
  data : vec nat8;
};

// canister::method_name **must**
// conform to `func (vec event) -> ();`
type subscribe_request = record {
  method_name : text;
  canister : principal;
}

type subscription_result = variant {
  Ok;
  Err : record {message : text};
};

type canister_event_information = record {
  event_schema_link : text;
};

service : {
    dl_subscribe_simple : (subscribe_request) -> (subscription_result);
    dl_remove_simple_subscription : (subscribe_request) -> ();
    dl_canister_events_information : () -> ();
}
```

## Motoko Types

```motoko
type Event = {
  creator : ?Principal;
  source_method : Text;
  created_at : Nat64;
  data : [Nat8];
};

// canister::method_name **must**
// conform to `func (vec event) -> ();`
type SubscribeRequest = {
  method_name : Text;
  canister : Principal;
};

type SubscriptionResult = {
  #Ok;
  #Err : { message : Text };
};
```

## Rust Types 

```rust 
use ic_cdk::export::Principal;
use ic_cdk::export::candid::CandidType;
use serde::{Serialize, Deserialize};

#[derive(Clone, Debug, Deserialize, Serialize)]
pub struct Event {
    creator: Option<Principal>,
    source_method: String,
    created_at: u64,
    data: Vec<u8>,
}

#[derive(Clone, Debug, Deserialize, Serialize)]
pub struct SubscribeRequest {
    method_name: String,
    canister: Principal,
}

#[derive(Clone, Debug, Deserialize, Serialize)]
pub enum SubscriptionResult {
    Ok,
    Err { message: String },
}
```