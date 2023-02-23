# Departure Labs DLIP 4 | Call Proxy

```
version : v0
interface_identifier : DL::DLIP_3v0
```

## References
- [Cycles Wallet](https://github.com/dfinity/cycles-wallet) 
- [ERC725](https://github.com/ethereum/EIPs/issues/725)

## Summary
This is an interface standard describing a call proxy canister facilitating the calling other services.

## Rationale
Smart contract wallets can enable programmatic control of contract calls. 

## Candid
```candid
type CanisterCallRequest = record {
  canister : principal;
  method : text;
  cycles : nat64;
  args : vec nat8;
};

type RejectionCode = variant {
  "NoError": null,
  "SysFatal": null,
  "SysTransient": null,
  "DestinationInvalid": null,
  "CanisterReject": null,
  "CanisterError": null,
  "Unknown": null,
};

type CallFailure = variant {
  "NotAuthorized": null,
  "Blocked": record { reason: text },
  "Error": record { code: RejectionCode, message: text },
};

type CallResult = variant {
  Ok: vec nat8;
  Err: CallFailure;
};

service : {
  dl_call_blocking_v0: (CanisterCallRequest) -> (CallResult)
}
```





















