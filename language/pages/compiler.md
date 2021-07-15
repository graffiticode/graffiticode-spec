# Compiler
A compiler is defined by a name, lexicon, and compile function. Optionally, compilers can provide additional functionality for display via a [React](https://reactjs.org/) component (class or functional) that can be loaded remotely (see [`RemoteComponent`](https://github.com/Paciolan/remote-component) for how this could be done).

## Endpoints
A compiler exposes a HTTP/JSON api.

### Required Endpoints
- Name
  - Endpoint: `/`
  - Method: `GET`
  - Result: The name of the language
    - _language_: `String` - a string matching the regex `^L\d+$`.
  - __Example__:
```bash
curl https://l0.artcompiler.com/
HTTP/1.1 200 OK
Content-Type: application/json
{"language": "L0"}
```
- Lexicon
  - Endpoint: `/lexicon`
  - Method: `GET`
  - Result: The lexicon that defines the language
    - _keyword_: `String`
      - _tk_: `Number`
      - _name_: `String` - name of the AST node
      - _cls_: `String` - type of primative
      - _length_: `Number` - number of arguments
      - _arity_: `Number`
  - __Example__:
```bash
curl https://l0.artcompiler.com/lexicon
HTTP/1.1 200 OK
Content-Type: application/json
{"hello":{"tk":1,"name":"HELLO","cls":"function","length":1,"arity":1}}
```
- Compile
  - Endpoint: `/compile`
  - Method: `POST`
  - Required parameters:
    - _context_: `Object` - The context of the compile request
      - _auth_: `Object` - AuthN/AuthZ of the requester
      - _options_: `Object` - (Optional) Configuration to use for the compile
    - _code_: `Object` - AST to compile
    - _data_: `Object` - (Optional) Data to be used for the compile
  - Result: The resulting object code of the compile
    - _obj_: `any`
  - __Example__:
```bash
curl -X POST -H 'Content-Type: application/json' -d '{"context": {"auth": {"token": "supersecret"}}, "code": {"root": 0}}' https://l0.artcompiler.com/compile
HTTP/1.1 200 OK
Content-Type: application/json
{"obj": 42}
```

### Optional Endpoints
- Viewer
  - Endpoint: `/viewer.js`
  - Method: `GET`
  - Result: The React component viewer for the component
    - _\<Viewer /\>_: `application/javascript`
  - __Example__:
```bash
curl https://l0.artcompiler.com/viewer.js
HTTP/1.1 200 OK
Content-Type: application/javascript; charset=UTF-8
(()=>{"use strict";var t={418:t=>{var n=Object.getOwnPropertySymbols,e...
```