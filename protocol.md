# Protocol

The tenthbit protocol is a JSON chat protocol that supports the following
advantages over IRC:

- Required SSL for clients and server-links
- Optional GZIP compression when supported and beneficial
- Federated, decentralized link-paths
- A baked-in account system

# Federation

At the moment, only the account system is federated (communications may be
federated later). Essentially, an account that exists on one server (the
account's "home" server) can be used to authenticate against other servers
(servers which are "foreign" to that account). Once authentication succeeds,
foreign servers are not required to fully authorize the nonlocal user as a
local user.

Accounts are specified using an email-style notation; a user `danopia` on
a server located at `10b.it` would be globally addressable by
`danopia@10b.it`. However, other users accessing the `10b.it` server may
refer to the same user with simply `danopia`. Bare usernames act the same
as usernames with the current server's name appended.

## Foreign authentication flow
A ticket-based system is used to prevent credentials from flowing through
potentially untrusted foreign servers. An alphanumeric ticket is generated
by an account's home server via an established connection as per the user's
request at any time, and must expire at some point in the future. [TODO: Pair
a ticket with a certain remote server, and only let it work when presented by
that certain server.]

A valid ticket may be presented to a foreign server during the authentication
flow, along with the ticket's home server. For example, `danopia@10b.it`
received the ticket `OHMY`. `danopia` may connect to `somewhere.else` and
request authentication with `{ticket: "OHMY", server: "10b.it"}`.
`somewhere.else` SHOULD connect to `10b.it` (if not already connected) and
relay the ticket. `10b.it` must reply with the corresponding username
(in this case, `danopia`) if the ticket was valid, or an error otherwise.
If the ticket existed, `10b.it` MUST invalidate it so that further redemption
attempts with the same ticket will fail.
