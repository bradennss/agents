# Exercising a backend with curl

Hit the real running endpoint the way a client would and read back the response. That's how you find out the route is wired, the middleware runs, the serializer works, and the database write lands. Tests around the handler won't tell you those.

## Start the real service

Bring the service up the way the project does: its dev command, its compose file, its run script. It needs its real dependencies, so a real database and any queues or caches it talks to. Note the base URL and port. If startup needs env vars or migrations, run them first, because a service running against an empty or stale database will give you wrong answers.

## See the full response

Check the status code and headers, not just the body. A few flags help:

- `-i` includes the response headers.
- `-s` silences the progress meter.
- `-w '\n%{http_code}\n'` prints the status code on its own line.
- `-D -` dumps headers to stdout while the body goes where you send it.

```sh
curl -i -s http://localhost:3000/api/health
```

Pipe JSON through `jq` so you can read it:

```sh
curl -s http://localhost:3000/api/users/1 | jq .
```

## Send a real request body

Set the content type and send JSON as the body:

```sh
curl -i -s -X POST http://localhost:3000/api/users \
  -H 'Content-Type: application/json' \
  -d '{"name":"Ada","email":"ada@example.com"}'
```

For a full round trip, pull a value out of one response and pass it to the next call:

```sh
id=$(curl -s -X POST http://localhost:3000/api/users \
  -H 'Content-Type: application/json' \
  -d '{"name":"Ada"}' | jq -r .id)
curl -s http://localhost:3000/api/users/"$id" | jq .
```

## Handle auth like a client

Get a real token or session through the real login path, then send it. Don't skip auth for the test, because the auth path is often part of what you changed.

```sh
token=$(curl -s -X POST http://localhost:3000/api/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"ada@example.com","password":"secret"}' | jq -r .token)
curl -s http://localhost:3000/api/me -H "Authorization: Bearer $token" | jq .
```

For cookie sessions, save and reuse the jar with `-c cookies.txt` to store and `-b cookies.txt` to send.

## Cover the paths you changed

Start with the happy path, then hit what the change affected:

- Bad input: missing fields, wrong types, malformed JSON. Confirm the status is a 4xx and the error body is what you designed.
- Missing or bad auth: no token, an expired token, another user's token. Confirm it's rejected, not quietly allowed.
- Not found: an id that doesn't exist. Confirm a 404, not a 500.
- Conflict and duplicates: create the same thing twice, write against a stale version. Confirm the conflict is handled.
- Boundaries: empty collections, the first and last page, the largest input you allow.

## Check the resulting state

A 200 doesn't tell you the write went in correctly. After a write, read it back through another endpoint or query the database, and confirm the stored state is right. If the change emits an event or enqueues a job, confirm that landed too. The response and the state both have to match what you meant.

## Show the proof

Paste the command and the real response, including the status code, for each path you drove. The reader should see the request, the status, and the body.
