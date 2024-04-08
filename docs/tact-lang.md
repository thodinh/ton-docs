<!-- Generated on 2024-04-08T14:14:45.961Z -->

<!-- site: docs.tact-lang.org/index.html -->
# Learn all about programming in ⚡ Tact – TactGitHub
Tact Language Documentation
# Learn all about programming in ⚡ Tact

![Tact banner](/_next/static/media/banner.0c18b672.jpeg)

Tact is a new programming language for TON Blockchain that is focused on efficiency and simplicity. It is designed to be easy to learn and use, and to be a good fit for smart contracts. Tact is a statically typed language with a simple syntax and a powerful type system.

## Let's start![](#start)

### Ensure that Node.js LTS is installed and available[](#start-1)

To check it, run `node --version` — it should show you the version 18.0.0 or later.

### Run the following command[](#start-2)

It will create a new project with the simple counter contract:

npmyarnpnpm

```
npm create ton -- simple-counter --type tact-counter --contractName SimpleCounter
```

### That's it![](#start-3)

Your first contract project is written and compiled already! Go check it out by moving into the relevant directory — `cd simple-counter/contracts`.

Here's how it would look like:

```
import "@stdlib/deploy";
 
message Add {
    queryId: Int as uint64;
    amount: Int as uint32;
}
 
contract SimpleCounter with Deployable {
    id: Int as uint32;
    counter: Int as uint32;
 
    init(id: Int) {
        self.id = id;
        self.counter = 0;
    }
 
    receive(msg: Add) {
        self.counter += msg.amount;
    }
 
    get fun counter(): Int {
        return self.counter;
    }
 
    get fun id(): Int {
        return self.id;
    }
}
```

To re-compile or deploy, refer to the commands in the scripts section of `package.json` in the root of this newly created project and to the documentation of [Blueprint (opens in a new tab)](https://github.com/ton-org/blueprint) — this is the tool we've used to create and compile your first simple counter contract in Tact. In fact, Blueprint can do much more than that: including tests, customizations and more.

## Where to go next?[](#next)

### Have some blockchain knowledge already?[](#next-1)

See the [Tact Cookbook](/cookbook), which is a handy collection of everyday tasks (and solutions) every Tact developer faces during smart contract development. Use it to avoid re-inventing the wheel.

Alternatively, check the following cheatsheets to quickly get started:

[💎 Coming from FunC (TON)](/book/cs/from-func)[🔷 Coming from Solidity (Ethereum)](/book/cs/from-solidity)

### Want to know more?[](#next-2)

For further guidance on compilation, testing and deployment see the [Getting started](/book/guides/getting-started) guide.

For custom plugins for your favorite editor and other tooling see the [Tools](/ecosystem/tools/overview) page.

Alternatively, take a look at the following broader sections:

* [Book](/book) helps you learn the language step-by-step
* [Cookbook](/cookbook) gives you ready-made recipes of Tact code
* [Language](/language) provides a complete reference of the standard library, grammar and evolution process
* Finally, [Ecosystem](/ecosystem) describes "what's out there" in the Tacts' and TONs' ecosystems

[📚 Read the Book of Tact](/book)[🍲 Examine the Cookbook](/cookbook)[🔬 Study the Language](/language)[🗺️ Embrace the Ecosystem](/ecosystem)

### Feeling a bit uncomfortable?[](#next-3)

If you ever get stuck, don't hesitate to reach out to Tact's flourishing community:

[✈️ Telegram Group→](https://t.me/tactlang)[🐦 X/Twitter→](https://twitter.com/tact%5Flanguage)

Good luck on your coding adventure with ⚡ Tact!
<!-- site: docs.tact-lang.org/ecosystem -->
# Overview – TactGitHub
Tact Language Documentation
Ecosystem

Overview

# Overview

Welcome to the **Ecosystem** section — a bird-eye overview of Tact ecosystem, tools and ways you can start contributing to those!

Here are it's main contents:

### Tools[](#tools)

[Tools](/ecosystem/tools/overview) sub-section is a list of official and community-made tools made specifically for Tact, or whose that play along with the language and other tools. Each tool has a brief usage details and additional information, which sometimes is missing from the respective docs or is a convenient summary available only in the Tact documentation.

[Go to tools overview→](/ecosystem/tools/overview)
<!-- site: docs.tact-lang.org/evolution/OTP-004 -->
# Redirecting...
undefined

<!-- site: docs.tact-lang.org/language/spec -->
# Tact Specification – TactGitHub
Tact Language Documentation
Language

Specification

# Tact Specification

🚨

This page is mostly a stub until it's implemented in [#76 (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues/76)

Tact grammar used in its compiler is written in an [Ohm language (opens in a new tab)](https://ohmjs.org), which is based on [parsing expression grammars (opens in a new tab)](http://en.wikipedia.org/wiki/Parsing%5Fexpression%5Fgrammar) (PEGs), which are a formal way of describing syntax, similar to regular expressions and context-free grammars.

```
Tact {
 
    // Starting point of the program
    Program = ProgramItem*
    ProgramItem = Struct
                | Contract
                | Primitive
                | StaticFunction
                | NativeFunction
                | ProgramImport
                | Trait
                | Constant
    ProgramImport = import stringLiteral ";"
 
    // Built-in declarations
    Primitive = "primitive" Type ";"
 
    // Static function
    StaticFunction = Function
    NativeFunction = nameAttribute "(" funcId ")" FunctionAttribute* native id "(" ListOf<FunctionArg,","> ")" ";" --withVoid
                   | nameAttribute "(" funcId ")" FunctionAttribute* native id "(" ListOf<FunctionArg,","> ")" ":" Type ";" --withType
 
    // Field declarations
    Type = typeLiteral "?" --optional
         | typeLiteral --required
         | "map" "<" typeLiteral (as id)? "," typeLiteral (as id)? ">" --map
         | "bounced" "<" typeLiteral ">" --bounced
    Field = id ":" Type ";" --default
          | id ":" Type "=" Expression ";" --defaultWithInit
          | id ":" Type as id ";" --withSerialization
          | id ":" Type as id "=" Expression ";" --withSerializationAndInit
 
    // Constant
    ConstantAttribute = virtual    --virtual
                      | override   --override
                      | abstract   --abstract
    Constant = ConstantAttribute* ~fun const id ":" Type "=" Expression ";" --withValue
             | ConstantAttribute* ~fun const id ":" Type ";"                --withEmpty
 
    // Struct
    Struct = "struct" typeLiteral "{" StructBody* "}" --originary
           | "message" typeLiteral "{" StructBody* "}" --message
           | "message" "(" integerLiteral ")" typeLiteral "{" StructBody* "}" --messageWithId
    StructBody = Field
 
    // Contract
    Contract = ContractAttribute* contract id "{" ContractBody* "}" --simple
             | ContractAttribute* contract id with ListOf<id,","> "{" ContractBody* "}" --withTraits
    ContractInit = "init" "(" ListOf<FunctionArg,","> ")" "{" Statement* "}"
    ContractBody = Field
                 | ContractInit
                 | ReceiveFunction
                 | Function
                 | Constant
 
    // Trait
    Trait = ContractAttribute* trait id "{" TraitBody* "}" --originary
          | ContractAttribute* trait id with ListOf<id,","> "{" TraitBody* "}" --withTraits
    TraitBody = Field
              | ReceiveFunction
              | Function
              | Constant
 
    // Contract attributes
    ContractAttribute = "@interface" "(" stringLiteral ")" --interface
 
    // Function
    FunctionAttribute = "get"     --getter
                      | mutates   --mutates
                      | extends   --extends
                      | virtual   --virtual
                      | override  --override
                      | inline    --inline
                      | abstract  --abstract
    Function = FunctionAttribute* fun id "(" ListOf<FunctionArg,","> ")" "{" Statement* "}" --withVoid
             | FunctionAttribute* fun id "(" ListOf<FunctionArg,","> ")" ":" Type "{" Statement* "}" --withType
             | FunctionAttribute* fun id "(" ListOf<FunctionArg,","> ")" ";" --abstractVoid
             | FunctionAttribute* fun id "(" ListOf<FunctionArg,","> ")" ":" Type ";" --abstractType
    FunctionArg = id ":" Type
 
    ReceiveFunction = "receive" "(" FunctionArg ")" "{" Statement* "}" --simple
                    | "receive" "(" ")" "{" Statement* "}" --empty
                    | "receive" "(" stringLiteral ")" "{" Statement* "}" --comment
                    | "bounced" "(" FunctionArg ")" "{" Statement* "}" --bounced
                    | "external" "(" FunctionArg ")" "{" Statement* "}" --externalSimple
                    | "external" "(" stringLiteral ")" "{" Statement* "}" --externalComment
                    | "external" "(" ")" "{" Statement* "}" --externalEmpty
 
    // Statements
    Statement = StatementLet
              | StatementBlock
              | StatementReturn
              | StatementExpression
              | StatementAssign
              | StatementAugmentedAssign
              | StatementCondition
              | StatementWhile
              | StatementRepeat
              | StatementUntil
    StatementBlock = "{" Statement* "}"
    StatementLet = let id ":" Type "=" Expression ";"
    StatementReturn = return Expression ";" --withExpression
                    | return ";" --withoutExpression    
    StatementExpression = Expression ";"
    StatementAssign = LValue "=" Expression ";"
    StatementAugmentedAssign = StatementAugmentedAssignAdd
                             | StatementAugmentedAssignSub
                             | StatementAugmentedAssignMul
                             | StatementAugmentedAssignDiv
                             | StatementAugmentedAssignRem
    StatementAugmentedAssignAdd = LValue "+=" Expression ";"
    StatementAugmentedAssignSub = LValue "-=" Expression ";"
    StatementAugmentedAssignMul = LValue "*=" Expression ";"
    StatementAugmentedAssignDiv = LValue "/=" Expression ";"
    StatementAugmentedAssignRem = LValue "%=" Expression ";"
    StatementCondition = if Expression "{" Statement* "}" ~else --simple
                       | if Expression "{" Statement* "}" else "{" Statement* "}" --withElse
                       | if Expression "{" Statement* "}" else StatementCondition --withElseIf
    StatementWhile = while "(" Expression ")" "{" Statement* "}"
    StatementRepeat = repeat "(" Expression ")" "{" Statement* "}"
    StatementUntil = do "{" Statement* "}" until "(" Expression ")" ";"
 
    // L-value
    LValue = id "." LValue --more
           | id --single
 
    // Expressions
    Expression = ExpressionConditional
    ExpressionConditional = ExpressionOr "?" ExpressionOr ":" ExpressionConditional --ternary
                          | ExpressionOr
    ExpressionOr = ExpressionOr "||" ExpressionAnd --or
                 | ExpressionAnd
    ExpressionAnd = ExpressionAnd "&&" ExpressionCompare --and
                  | ExpressionCompare
    ExpressionCompare = ExpressionCompare "!=" ExpressionBinary --not
                      | ExpressionCompare "==" ExpressionBinary --eq
                      | ExpressionCompare ">" ExpressionBinary --gt
                      | ExpressionCompare ">=" ExpressionBinary --gte
                      | ExpressionCompare "<" ExpressionBinary --lt
                      | ExpressionCompare "<=" ExpressionBinary --lte
                      | ExpressionBinary
    ExpressionBinary = ExpressionBinary ">>" ExpressionAdd --shr
                    | ExpressionBinary "<<" ExpressionAdd --shl
                    | ExpressionBinary "&" ExpressionAdd --bin_and
                    | ExpressionBinary "|" ExpressionAdd --bin_or
                    | ExpressionAdd
    ExpressionAdd = ExpressionAdd "+" ~"+" ExpressionMul --add
                  | ExpressionAdd "-" ~"-" ExpressionMul --sub
                  | ExpressionMul
    ExpressionMul = ExpressionMul "*" ExpressionUnary --mul
                  | ExpressionMul "/" ExpressionUnary --div
                  | ExpressionMul "%" ExpressionUnary --rem
                  | ExpressionUnary
    ExpressionUnary = "-" ExpressionUnarySuffix --neg
                    | "+" ExpressionUnarySuffix --add
                    | "!" ExpressionUnarySuffix --not
                    | ExpressionUnarySuffix
    ExpressionUnarySuffix = ExpressionValue "!!" --notNull
                          | ExpressionValue
    ExpressionBracket = "(" Expression ")"
 
    // Order is important
    ExpressionValue = ExpressionCall
                    | ExpressionField
                    | ExpressionStaticCall
                    | ExpressionBracket
                    | ExpressionNew
                    | integerLiteral
                    | boolLiteral
                    | id
                    | null
                    | ExpressionInitOf
                    | ExpressionString
    ExpressionString = stringLiteral
    ExpressionField = ExpressionValue "." id ~"("
    ExpressionCall = ExpressionValue "." id "(" ListOf<Expression, ","> ")"
    ExpressionNew = id "{" ListOf<NewParameter, ","> "}"
    NewParameter = id ":" Expression
    ExpressionStaticCall = id "(" ListOf<Expression, ","> ")"
    ExpressionInitOf = initOf id "(" ListOf<Expression, ","> ")"
 
    // Type Literal
    typeLiteral = letterAsciiUC typeLiteralPart*
    typeLiteralPart = letterAscii | digit | "_"
 
    // Integer Literal
    // hexDigit defined in Ohm's built-in rules (otherwise: hexDigit = "0".."9" | "a".."f" | "A".."F")
    // digit defined in Ohm's built-in rules (otherwise: digit = "0".."9")
    integerLiteral = integerLiteralHex | integerLiteralBin | integerLiteralOct | integerLiteralDec // Order is important
    integerLiteralDec = nonZeroDigit ("_"? digit)*  --nonZeroIntegerLiteralDec
                      | "0" digit*                  --integerLiteralWithLeadingZero
    integerLiteralHex = ("0x" | "0X") hexDigit ("_"? hexDigit)*
    integerLiteralBin = ("0b" | "0B") binDigit ("_"? binDigit)*
    integerLiteralOct = ("0o" | "0O") octDigit ("_"? octDigit)*
    binDigit = "0" | "1"
    octDigit = "0".."7"
    nonZeroDigit = "1".."9"
 
    // Letters
    letterAsciiLC = "a".."z"
    letterAsciiUC = "A".."Z"
    letterAscii = letterAsciiLC | letterAsciiUC
    letterComment = letterAsciiLC | letterAsciiUC | digit | "_"
 
    // ID Literal
    idStart = letterAscii | "_"
    idPart = letterAscii | digit | "_"
    id = ~reservedWord #idStart #(idPart*)
 
    // FunC id
    funcLetter = letterAscii | "_" | "'" | "?" | "!" | "::" | "&"
    funcId = funcLetter #(funcLetter | digit)*
 
    // Bool Literal
    boolLiteral = ("true" | "false") ~idPart
 
    // String literal
    stringLiteralCharacter = ~("\"" | "\\" | lineTerminator) any
    stringLiteral = "\"" stringLiteralCharacter* "\""
 
    // Keywords
    // NOTE Order is important
    keyword = fun
            | let
            | return
            | extend
            | native
            | public
            | null
            | if
            | else
            | while
            | repeat
            | do
            | until
            | as
            | mutates
            | extends
            | import
            | with
            | trait
            | initOf
            | override
            | abstract
            | virtual
            | inline
            | const
    contract = "contract" ~idPart
    let = "let" ~idPart
    fun = "fun" ~idPart
    return = "return" ~idPart
    extend = "extend" ~idPart
    native = "native" ~idPart
    public = "public" ~idPart
    null = "null" ~idPart
    if = "if" ~idPart
    else = "else" ~idPart
    while = "while" ~idPart
    repeat = "repeat" ~idPart
    do = "do" ~idPart
    until = "until" ~idPart
    as = "as" ~idPart
    mutates = "mutates" ~idPart
    extends = "extends" ~idPart
    import = "import" ~idPart
    with = "with" ~idPart
    trait = "trait" ~idPart
    initOf = "initOf" ~idPart
    virtual = "virtual" ~idPart
    override = "override" ~idPart
    inline = "inline" ~idPart
    const = "const" ~idPart
    abstract = "abstract" ~idPart
 
    // Attributes
    nameAttribute = "@name"
 
    // Reserved
    reservedWord = keyword
 
    // Comments
    space += comment | lineTerminator
    comment = multiLineComment | singleLineComment
    lineTerminator = "\n" | "\r" | "\u2028" | "\u2029"
    multiLineComment = "/*" (~"*/" any)* "*/"
    singleLineComment = "//" (~lineTerminator any)*
}
```
<!-- site: docs.tact-lang.org/language/guides/masterchain -->
# Redirecting...
undefined

<!-- site: docs.tact-lang.org/language/evolution/overview -->
# Overview – TactGitHub
Tact Language Documentation
Language

Evolution

Overview

# Overview

This sub-section contains all standards that are defined by the Tact Foundation and are used in the evolution process of the Tact and TON ecosystem. Additionaly, it features TEPs (TON Enhancement Proposals) and the up-to-date changelog of Tact updates.

## Open Tact Proposals (OTPs)[](#open-tact-proposals-otps)

[OTP-001→](/language/evolution/OTP-001)[OTP-002→](/language/evolution/OTP-002)[OTP-003→](/language/evolution/OTP-003)[OTP-004→](/language/evolution/OTP-004)[OTP-005→](/language/evolution/OTP-005)[OTP-006→](/language/evolution/OTP-006)

## TON Enhancement Protocols (TEPs)[](#ton-enhancement-protocols-teps)

The main goal of TON Enhancement Proposals is to provide a convenient and formal way to propose changes to TON Blockchain and standardize ways of interaction between different parts of ecosystem. Proposal management is done using GitHub pull requests, the process is described formally in [TEP-1 (opens in a new tab)](https://github.com/ton-blockchain/TEPs/blob/master/text/0001-tep-lifecycle.md).

List of [merged TEPs (opens in a new tab)](https://github.com/ton-blockchain/TEPs#merged-teps).

## Changelog[](#changelog)

All notable changes to the main Tact repository are documented in the [CHANGELOG.md (opens in a new tab)](https://github.com/tact-lang/tact/blob/main/CHANGELOG.md).
<!-- site: docs.tact-lang.org/language/evolution/OTP-001 -->
# OTP-001: Supported Interfaces – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-001: Supported Interfaces

# OTP-001: Supported Interfaces

This proposal recommends a way to introspect smart contracts and find out what interfaces they support.

## Motivation[](#motivation)

Right now it is impossible to guess what a user wants to do with a contract or can't figure out what the transaction is about because there is no clear way to find what a contract is about. Humans need to remember or guess what this was about in most ways.

## Guide[](#guide)

When human tries to sign a transaction, they need to understand clearly what they are doing: minting, token transfer, staking, DAO voting. While Ethereum wallets support signing arbitrary structures it is still not clear what are you signing and what's the implications of doing so. In the same way, explorers can't show what's going on in a nice form.

The start of a working with specific contract is a performing introspection - figuring out what the contract declares about itself. When the app knows what this contract is about it could build a good UI, show transaction history, and verify what a human tries to sign.

This proposal describes a way to report what interfaces the contract supports.

Interfaces are defined in a free-form specification. Unlike most of the other approaches, this proposal defines interface as not only the technical interface of a contract (get methods, internal messages, etc) but also a description of its behavior. Attaching a hash of the representation of a technical interface of a contract could cause conflicts between different standards and because of this proposal defines interfaces loosely. Also, it allows an interface to be more fluid, for example token that couldn't be transferred could be just a contract that will have to get the method `can_transfer` that returns `false` and this would mean that this token doesn't support transfers at all without the need to implement this method.

Interface IDs are hashes of reverse domain names (like packages in Java), this avoids clashes of names between different teams if they want to build something just for themselves.

## Specification[](#specification)

In order to support the introspection contract MUST implement the supports\_interface GET method:

`(int...) supported_interfaces()`Which returns a list of supported interface codes. The first value MUST be `hash("org.ton.introspection.v0")` \= `123515602279859691144772641439386770278`. If the first value is incorrect app MUST stop attempting to introspect the contract. Example

```
_ supported_interfaces() method_id {
    return (123515602279859691144772641439386770278);
}
```

The hash of an interface is defined as truncated to 128 bits SHA256.

## Drawbacks[](#drawbacks)

This proposal doesn't guarantee that the contract would behave correctly to an interface, also it doesn't provide a guaranteed way to avoid name clashes between different interfaces. This is a non-goal for this proposal.

This proposal isn't tied to a specific technical interface. This could lead to multiple interfaces that do the same thing but with different IDs. This is a non-goal for this proposal since a centralized registry would be very useful for existing interfaces and a custom one would be used mostly in-house.

## Rationale and alternatives[](#rationale-and-alternatives)

* Why 128 bit? We are looking at a global namespace that we need to keep without conflicts, we can't use anything much smaller since the probability of conflicts would be much higher. We are looking at UUID-like entropy that is exactly 128-bit and is time-proven. More than 128 is too wasteful.
* Why freeform? As mentioned before, it is easier just to define some ID to start work early and then eventually build a standard. Also interfaces (like ERC20) usually not just a technical interface, but also a number of rules on how to work with it.
* Why not find out what contract supports by decompiling? Explicit is always better than implicit in open-world scenarios. We can't rely on our "disassembling" capabilities to perform introspections, even small errors could be fatal.
* Why not hash of representation? Right now there are no compilers that support that, also this proposal is future-proof. If anyone would want to build something more automated they could easily build their own hashes by their own rules keeping everything the same for external observers.

## Prior art[](#prior-art)

[Ethereum Interface Detection (opens in a new tab)](https://eips.ethereum.org/EIPS/eip-165)
<!-- site: docs.tact-lang.org/language/evolution/OTP-002 -->
# OTP-002: Contract ABI – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-002: Contract ABI

# OTP-002: Contract ABI

ABI defines how to communicate with smart contracts. It contains information about the contract's receivers, data structures, etc.

## Motivation[](#motivation)

ABI is an essential tool that allows developers to generate handy bindings, UIs, etc. One of the best consumer usages would be using a DAO and being able to confirm what exactly it is trying to do before signing a transaction.

## Guide[](#guide)

This OTP is based on types that are defined in TLB+ and it is advised to know them before reading this OTP.

## Specification[](#specification)

ABI is a JSON file:

```
{
  "name": "MyContract",
  "types": [
    {
      "name": "MyRequest",
      "header": 12315123,
      "fields": [
        {
          "name": "queryId",
          "type": {
            "kind": "simple",
            "type": "int",
            "optional": false,
            "format": "uint256"
          }
        }
      ]
    }
  ],
  "receivers": [
    { "type": "binary", "kind": "internal", "name": "MyRequest" },
    { "type": "binary", "kind": "internal" },
    { "type": "comment", "kind": "internal", "comment": "Vote!" },
    { "type": "comment", "kind": "internal" },
    { "type": "empty", "kind": "internal" }
  ],
  "getters": [
    { "name": "getOwner", "type": "address", "args": [] },
    {
      "name": "getBalance",
      "type": "coins",
      "args": [
        {
          "name": "invested",
          "type": {
            "kind": "simple",
            "type": "uint",
            "format": "coins"
          }
        }
      ]
    }
  ],
  "errors": {
    "123": "Error description",
    "124": "Division by zero"
  }
}
```

## Drawbacks[](#drawbacks)

* Binary and compact representation of ABI could be better, but it is not critical for now.

## Prior art[](#prior-art)

* OTP-001, that is complimentary to this OTP.
<!-- site: docs.tact-lang.org/language/evolution/OTP-003 -->
# OTP-003: Self-ABI reporting – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-003: Self-ABI reporting

# OTP-003: Self-ABI reporting

This proposal defines how to report the contract's ABI using the IPFS link.

## Motivation[](#motivation)

Usually, ABI is supplied separately using a third-party service or via some repository on GitHub. This proposal suggests adding a new self-reporting of the contract's ABI using a link to an IPFS. This would allow us to avoid any third-party dependency and allow anyone to build tools that rely on ABI such as explorers, wallets, etc.

## Specification[](#specification)

To support this proposal, the contract should implement OTP-001 and report an interface `org.ton.abi.ipfs.v0`. Then implement a get method `get_abi_ipfs` that returns a string with an IPFS link to the ABI file. The link should be in the format `ipfs://<hash>`.

## Drawbacks[](#drawbacks)

* No way to upgrade ABI without updating a contract. This is a drawback exists only for hardcoded links.
<!-- site: docs.tact-lang.org/language/evolution/OTP-004 -->
# OTP-004: Auto Encoder – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-004: Auto Encoder

# OTP-004: Auto Encoder

This proposal defines a way to automatically build a serialization layout for a given structure.

## Motivation[](#motivation)

Designing a serialization layout in TLB is a very risky task. Developers have to take care of the size limitations of cells and remember how many bits are used by each field. This is a very error-prone task and it is very easy to make a mistake. This proposal aims to solve this problem by providing a way to automatically build a serialization layout for a given structure.

## Specification[](#specification)

We define auto-encoder as an eager algorithm that builds a serialization layout for a given structure. The algorithm is defined as follows:

```
Define available references and bits in a current cell 
   as `available_references` and `available_bits` respectively.
   NOTE: there must be at least one reference reserved for the serialization tail and one 
         bit for an optional flag. Depending on context more references or bits may be reserved. 

For each field in A:
    (size_bits, size_ref) = get_field_max_size(field);
    if (available_bits >= size_bits && available_references >= size_ref) {
        Push field to a current cell
    } else {
        available_references = (1023 - 1);
        available_bits = (4 - 1);
        Allocate a new tail and continue from the current field
    }
```

## Drawbacks[](#drawbacks)

* This is an implicit algorithm. It is not clear results of this allocator have to be checked to make compatible serialization.
<!-- site: docs.tact-lang.org/language/evolution/OTP-005 -->
# OTP-005: Argument-addressable contracts – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-005: Argument-addressable contracts

# OTP-005: Argument-addressable contracts

This proposal defines a way to address contracts by their arguments instead of their initial data.

## Motivation[](#motivation)

Init data could be very different from the arguments. This allows us to avoid executing untrusted code from another contract in the context of a current one or executing TVM code off-chain for deployment that could be risky in some cases.

## Specification[](#specification)

This specification defines a way to write arguments to an init data cell to be read by the contract code during deployment.

### Prefix[](#prefix)

The prefix is defined by a smart contract itself, but by default, it is assumed as a `single zero bit`. Prefix is used by the contract code to distinguish between deployed and not-deployed state.

### Arguments encoding[](#arguments-encoding)

Arguments are encoded using [Auto Encoder](/evolution/OTP-004).

### Contract Requirements[](#contract-requirements)

* Contract MUST expose `lazy_deployment_completed` get method that returns `true` if the contract is deployed and `false` otherwise.
* Contract MUST expose `org.ton.deploy.lazy.v0` interface.

## Drawbacks[](#drawbacks)

* Contracts could be in a semi-deployed state
* There are multiple ways to write arguments that would end up in a different init data and a different address
* You can deploy a pre-initialized contract and it would have a different address while being fully functional
* Unpredictable gas usage on deployment. Deployments are usually expensive, but this proposal makes it even more expensive.
<!-- site: docs.tact-lang.org/language/evolution/OTP-006 -->
# OTP-006: Contract Package – TactGitHub
Tact Language Documentation
Language

Evolution

OTP-006: Contract Package

# OTP-006: Contract Package

This proposal defines a way to package compile contracts, their dependencies, and all related metadata into a single file.

## Motivation[](#motivation)

A unified package format is needed to simplify the process of deploying and upgrading contracts using various tools without the need to configure them.

## Specification[](#specification)

The package file has an extension `.pkg` and is a JSON file:

```
{
  "name": "My Contract",
  "code": "... boc of code ...",
  "abi": "ABI string to be uploaded as is to IPFS or Ton Storage",
  "init": {
    "kind": "direct", // Means that this contract can be deployed as is
    "args": {
      // ... Arguments in ABI format
    },
    "prefix": {
      // Optional prefix for contract init state
      "bits": 0, // Number of bits in prefix
      "value": 0 // Value of prefix
    },
    "deployment": {
      "kind": "system-cell", // Means that this contract can be deployed as is
      "system": "... boc of system cell ..."
    }
  },
  "sources": {
    "file.ton": "... base64 encoded source file ..."
  },
  "compiler": {
    "name": "func",
    "version": "0.4.1",
    "parameters": "..." // Optional string parameters
  }
}
```

## Drawbacks[](#drawbacks)

None

## Reference[](#reference)

* Bags of Cells(BOC): [https://docs.ton.org/develop/data-formats/cell-boc#packing-a-bag-of-cells (opens in a new tab)](https://docs.ton.org/develop/data-formats/cell-boc#packing-a-bag-of-cells)
<!-- site: docs.tact-lang.org/cookbook/single-communication -->
# Single-contract Communication – TactGitHub
Tact Language Documentation
Cookbook

Single-contract communication

# Single-contract Communication

This page lists examples of communication of a single deployed contract with other contracts on blockchain.

For examples of communication between multiple deployed contracts see: [Multi-contract communication](/cookbook/multi-communication).

## How to make a basic reply[](#how-to-make-a-basic-reply)

```
receive() {
    self.reply("Hello, World!".asComment()); // asComment converts a String to a Cell with a comment
}
```

## How to send a simple message[](#how-to-send-a-simple-message)

```
send(SendParameters{
    bounce: true, // default
    to: destinationAddress,
    value: ton("0.01"), // attached amount of Tons to send
    body: "Hello from Tact!".asComment() // comment (optional)
});
```

## How to send a message with the entire balance[](#how-to-send-a-message-with-the-entire-balance)

If we need to send the whole balance of the smart contract, then we should use the `SendRemainingBalance` send mode. Alternatively, we can use `mode: 128`, which has the same meaning.

```
send(SendParameters{
    // bounce = true by default
    to: sender(), // send the message back to the original sender
    value: 0,
    mode: SendRemainingBalance, // or mode: 128
    body: "Hello from Tact!".asComment() // comment (optional)
});
```

## How to send a message with the remaining value[](#how-to-send-a-message-with-the-remaining-value)

If we want to make a reply to the same sender, we can use the mode `SendRemainingValue` (i.e. `mode: 64`), which carries all the remaining value of the inbound message in addition to the value initially indicated in the new message.

```
send(SendParameters{
    // bounce = true by default
    to: sender(), // send the message back to the original sender
    value: 0,
    mode: SendRemainingValue,
    body: "Hello from Tact!".asComment() // comment (optional)
});
```

It's often useful to add the `SendIgnoreErrors` flag too, in order to ignore any errors arising while processing this message during the action phaseL

```
send(SendParameters{
    // bounce = true by default
    to: sender(), // send the message back to the original sender
    value: 0,
    mode: SendRemainingValue | SendIgnoreErrors, // prefer using | over + for the mode
    body: "Hello from Tact!".asComment()         // comment (optional)
});
```

The latter example is identical to using a [.reply() function](/cookbook/single-communication#how-to-make-a-basic-reply).

## How to send a message with a long text comment[](#how-to-send-a-message-with-a-long-text-comment)

If we need to send a message with a lengthy text comment, we should create a [String](/book/types#primitive-types) that consists of more than 127127127 characters. To do this, we can utilize the [StringBuilder](/book/types#primitive-types) primitive type and its methods called `beginComment()` and `append()`. Prior to sending, we should convert this string into a cell using the `toCell()` method.

```
let comment: StringBuilder = beginComment();
let longString = "..."; // Some string with more than 127 characters.
str_builder.append(longString);
 
send(SendParameters{
    // bounce = true by default
    to: sender(),
    value: 0,
    mode: SendIgnoreErrors,
    body: longString.toCell(),
});
```

💡

**Useful links:**  
["Sending messages" in the Book](/book/send#send-message)  
["Message mode" in the Book](/book/message-mode) [StringBuilder in the Book](/book/types#primitive-types)  
[Cell in Language→Reference](/language/ref/cells)

🤔

Didn't find your favorite example of a single-contract communication? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/type-conversion -->
# Type conversion – TactGitHub
Tact Language Documentation
Cookbook

Type conversion

# Type conversion

This page shows examples of converting between [primitive types](/book/types#primitive-types) and obtaining them from [composite types](/book/types#composite-types).

## `Int` ↔ `String`[](#int-string)

### How to convert a `String` to an `Int`[](#how-to-convert-a-string-to-an-int)

```
// Defining a new extension function for type String that returns value of type Int
// Caution: produces unexpected results when String contains non-numeric characters!
extends fun toInt(self: String): Int {
    // Cast the String as a Slice for parsing
    let string: Slice = self.asSlice();
 
    // A variable to store the accumulated number
    let acc: Int = 0;
 
    // Loop until the String is empty
    while (!string.empty()) {
        let char: Int = string.loadUint(8); // load 8 bits (1 byte) from the Slice
        acc = (acc * 10) + (char - 48);     // using ASCII table to get numeric value
        // Note, that this approach would produce unexpected results
        //   when the starting String contains non-numeric characters!
    }
 
    // Produce the resulting number
    return acc;
}
 
fun runMe() {
    let string: String = "26052021";
    dump(string.toInt());
}
```

### How to convert an `Int` to a `String`[](#how-to-convert-an-int-to-a-string)

```
let number: Int = 261119911;
 
// Converting the [number] to a String
let numberString: String = number.toString();
 
// Converting the [number] to a float String,
//   where passed argument 3 is the exponent of 10^(-3) of resulting float String,
//   and it can be any integer between 0 and 76 including both ends
let floatString: String = number.toFloatString(3);
 
// Converting the [number] as coins to a human-readable String
let coinsString: String = number.toCoinsString();
 
dump(numberString); // "261119911"
dump(floatString);  // "261119.911"
dump(coinsString);  // "0.261119911"
```

💡

**Useful links:**  
[Int.toString() in Language→Reference](/language/ref/strings#inttostring)  
[Int.toFloatString() in Language→Reference](/language/ref/strings#inttofloatstring)  
[Int.toCoinsString() in Language→Reference](/language/ref/strings#inttocoinsstring)

🤔

Didn't find your favorite example of type conversion? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/data-structures -->
# Data structures – TactGitHub
Tact Language Documentation
Cookbook

Data structures

# Data structures

Data structures are data organization, management, and storage formats that are usually chosen for efficient access to data. More precisely, a data structure is a collection of data values, the relationships among them, and the functions or operations that can be applied to the data.

This page lists a handy collection of data structures implemented in Tact for your day-to-day needs and beyond.

All of the data structures listed here are made using the built-in [map<k, v\>](/book/maps) type. For the description and basic usage of maps see the [dedicated page in the Book](/book/maps).

## Array[](#array)

An [array (opens in a new tab)](https://en.wikipedia.org/wiki/Array%5F%28data%5Fstructure%29) is a data structure consisting of a continuous block of memory, which represents a collection of elements of same memory size, each identified by at least one array key or _index_.

Following example emulates an array using a [map<Int, v\>](/book/maps) wrapped in a [Struct](/book/structs-and-messages#structs), where `v` can be any of the [allowed value types](/book/maps#allowed-types) of the map:

```
import "@stdlib/deploy"; // for Deployable trait
 
struct Array {
    map: map<Int as uint16, Int>; // array of Int values as a map of Ints to Ints,
                                  // with serialization of its keys to uint16 to save space
    length: Int = 0;              // length of the array, defaults to 0
}
 
// Compile-time constant upper bound for our map representing an array.
const MaxArraySize: Int = 5_000; // 5,000 entries max, to stay reasonably far from limits
 
// Extension mutation function for adding new entries to the end of the array
extends mutates fun append(self: Array, item: Int) {
    require(self.length + 1 <= MaxArraySize, "No space in the array left for new items!");
 
    self.map.set(self.length, item); // set the entry (key-value pair)
    self.length += 1;                // increase the length field
}
 
// Extension mutation function for inserting new entries at the given index
extends mutates fun insert(self: Array, item: Int, idx: Int) {
    require(self.length + 1 <= MaxArraySize, "No space in the array left for new items!");
    require(idx >= 0, "Index of the item cannot be negative!");
    require(idx < self.length, "Index is out of array bounds!");
 
    // Move all items from idx to the right
    let i: Int = self.length; // not a typo, as we need to start from the non-existing place
    while (i > idx) {
        // Note, that we use !! operator as we know for sure that the value would be there
        self.map.set(i, self.map.get(i - 1)!!);
        i -= 1;
    }
 
    // And put the new item in
    self.map.set(idx, item); // set the entry (key-value pair)
    self.length += 1;        // increase the length field
}
 
// Extension function for getting the value at the given index
extends fun getIdx(self: Array, idx: Int): Int {
    require(self.length > 0, "No items in the array!");
    require(idx >= 0, "Index of the item cannot be negative!");
    require(idx < self.length, "Index is out of array bounds!");
 
    // Note, that we use !! operator as we know for sure that the value would be there
    return self.map.get(idx)!!;
}
 
// Extension function for returning the last value
extends fun getLast(self: Array): Int {
    require(self.length > 0, "No items in the array!");
 
    // Note, that we use !! operator as we know for sure that the value would be there
    return self.map.get(self.length - 1)!!;
}
 
// Extension mutation function for deleting and entry at the given index and returning its value
extends mutates fun deleteIdx(self: Array, idx: Int): Int {
    require(self.length > 0, "No items in the array to delete!");
    require(idx >= 0, "Index of the item cannot be negative!");
    require(idx < self.length, "Index is out of array bounds!");
 
    // Remember the value, which is going to be deleted
    let memorized: Int = self.map.get(idx)!!;
 
    // Move all items from idx and including to the left
    let i: Int = idx;
    while (i + 1 < self.length) {
        // Note, that we use !! operator as we know for sure that the value would be there
        self.map.set(i, self.map.get(i + 1)!!);
        i += 1;
    }
 
    self.map.set(self.length - 1, null); // delete the last entry
    self.length -= 1;                    // decrease the length field
 
    return memorized;
}
 
// Extension mutation function for deleting the last entry and returning its value
extends fun deleteLast(self: Array): Int {
    require(self.length > 0, "No items in the array!");
 
    // Note, that we use !! operator as we know for sure that the value would be there
    let lastItem: Int = self.map.get(self.length - 1)!!;
    self.map.set(self.length - 1, null); // delete the entry
    self.length -= 1;                    // decrease the length field
 
    return lastItem;
}
 
// Extension function for deleting all items in the Array
extends mutates fun deleteAll(self: Array) {
    self.map = emptyMap();
    self.length = 0;
}
 
// Global static function for creating an empty Array
fun emptyArray(): Array {
    return Array{map: emptyMap()}; // length defaults to 0
}
 
// Contract, with emulating an Array using the map
contract MapAsArray with Deployable {
    // Persistent state variables
    array: Array;
 
    // Constructor (initialization) function of the contract
    init() {
        self.array = emptyArray();
    }
 
    // Internal message receiver, which responds to a String message "append"
    receive("append") {
        // Add a new item
        self.array.append(42);
    }
 
    // Internal message receiver, which responds to a String message "delete_5h"
    receive("delete_5th") {
        // Remove the 5th item if it exists and reply back with its value, or raise an erroor
        self.reply(self.array.deleteIdx(4).toCoinsString().asComment()); // index offset 0 + 4 gives the 5th item
    }
 
    // Internal message receiver, which responds to a String message "del_last"
    receive("del_last") {
        // Remove the last item and reply back with its value, or raise an error
        self.reply(self.array.deleteLast().toCoinsString().asComment());
    }
 
    // Internal message receiver, which responds to a String message "get_last"
    receive("get_last") {
        // Reply back with the latest item in the array if it exists, or raise an error
        self.reply(self.array.getLast().toCoinsString().asComment());
    }
 
    // Internal message receiver, which responds to a String message "delete_all"
    receive("delete_all") {
        self.array.deleteAll();
    }
}
```

## Stack[](#stack)

A [stack (opens in a new tab)](https://en.wikipedia.org/wiki/Stack%5F%28abstract%5Fdata%5Ftype%29) is a data structure consisting of a collection of elements with two main operations:

* push, which adds an element to the end of the collection
* pop, which removes the most recently added element

Following example emulates a stack using a [map<Int, v\>](/book/maps) wrapped in a [Struct](/book/structs-and-messages#structs), where `v` can be any of the [allowed value types](/book/maps#allowed-types) of the map:

```
import "@stdlib/deploy"; // for Deployable trait
 
struct Stack {
    map: map<Int as uint16, Int>; // stack of Int values as a map of Ints to Ints,
                                  // with serialization of its keys to uint16 to save space
    length: Int = 0;              // length of the stack, defaults to 0
}
 
// Compile-time constant upper bound for our map representing an stack.
const MaxStackSize: Int = 5_000; // 5,000 entries max, to stay reasonably far from limits
 
// Extension mutation function for adding new entries to the end of the stack
extends mutates fun push(self: Stack, item: Int) {
    require(self.length + 1 <= MaxStackSize, "No space in the stack left for new items!");
 
    self.map.set(self.length, item); // set the entry (key-value pair)
    self.length += 1;                // increase the length field
}
 
// Extension mutation function for deleting the last entry and returning its value
extends mutates fun pop(self: Stack): Int {
    require(self.length > 0, "No items in the stack to delete!");
 
    // Note, that we use !! operator as we know for sure that the value would be there
    let lastItem: Int = self.map.get(self.length - 1)!!;
    self.map.set(self.length - 1, null); // delete the entry
    self.length -= 1;                    // decrease the length field
 
    return lastItem;
}
 
// Extension function for returning the last value
extends fun peek(self: Stack): Int {
    require(self.length > 0, "No items in the stack!");
 
    // Note, that we use !! operator as we know for sure that the value would be there
    return self.map.get(self.length - 1)!!;
}
 
// Extension function for deleting all items in the Stack
extends mutates fun deleteAll(self: Stack) {
    self.map = emptyMap();
    self.length = 0;
}
 
// Global static function for creating an empty Stack
fun emptyStack(): Stack {
    return Stack{map: emptyMap()}; // length defaults to 0
}
 
contract MapAsStack with Deployable {
    // Persistent state variables
    stack: Stack; // our stack, which uses the map
 
    // Constructor (initialization) function of the contract
    init() {
        self.stack = emptyStack();
    }
 
    // Internal message receiver, which responds to a String message "push"
    receive("push") {
        // Add a new item
        self.stack.push(42);
    }
 
    // Internal message receiver, which responds to a String message "pop"
    receive("pop") {
        // Remove the last item and reply with it
        self.reply(self.stack.pop().toCoinsString().asComment());
    }
 
    // Internal message receiver, which responds to a String message "peek"
    receive("peek") {
        // Reply back with the latest item in the map if it exists, or raise an error
        self.reply(self.stack.peek().toCoinsString().asComment());
    }
 
    // Internal message receiver, which responds to a String message "delete_all"
    receive("delete_all") {
        self.stack.deleteAll();
    }
 
    // Getter function for obtaining the stack
    get fun map(): map<Int, Int> {
        return self.stack.map;
    }
 
    // Getter function for obtaining the current length of the stack
    get fun length(): Int {
        return self.stack.length;
    }
}
```

## Circular buffer[](#circular-buffer)

A [circular buffer (opens in a new tab)](https://en.wikipedia.org/wiki/Circular%5Fbuffer) (circular queue, cyclic buffer or ring buffer) is a data structure, which uses a single, fixed-size [buffer (opens in a new tab)](https://en.wikipedia.org/wiki/Data%5Fbuffer) as it were connected end-to-end.

Following example emulates a circular buffer using a [map<Int, v\>](/book/maps) wrapped in a [Struct](/book/structs-and-messages#structs), where `v` can be any of the [allowed value types](/book/maps#allowed-types) of the map:

```
import "@stdlib/deploy"; // for Deployable trait
 
struct CircularBuffer {
    map: map<Int as uint8, Int>; // circular buffer of Int values as a map of Ints to Ints,
                                 // with serialization of its keys to uint8 to save space
    length: Int = 0;             // length of the circular buffer, defaults to 0
    start: Int = 0;              // current index into the circular buffer, defaults to 0
}
 
// Compile-time constant upper bound for our map representing a circular buffer.
const MaxCircularBufferSize: Int = 5;
 
// Extension mutation function for putting new items to the circular buffer
extends mutates fun put(self: CircularBuffer, item: Int) {
    if (self.length < MaxCircularBufferSize) {
        self.map.set(self.length, item); // store the item
        self.length += 1;                // increase the length field
    } else {
        self.map.set(self.start, item);                        // store the item, overriding previous entry
        self.start = (self.start + 1) % MaxCircularBufferSize; // update starting position
    }
}
 
// Extension mutation function for getting an item from the circular buffer
extends mutates fun getIdx(self: CircularBuffer, idx: Int): Int {
    require(self.length > 0, "No items in the circular buffer!");
    require(idx >= 0, "Index of the item cannot be negative!");
 
    if (self.length < MaxCircularBufferSize) {
        // Note, that we use !! operator as we know for sure that the value would be there
        return self.map.get(idx % self.length)!!;
    }
 
    // Return the value rotating around the circular buffer, also guaranteed to be there
    return self.map.get((self.start + idx) % MaxCircularBufferSize)!!;
}
 
// Extension function for iterating over all items in the circular buffer and dumping them to the console
extends fun printAll(self: CircularBuffer) {
    let i: Int = self.start;
    repeat (self.length) {
        dump(self.map.get(i)!!); // !! tells the compiler this can't be null
        i = (i + 1) % MaxCircularBufferSize;
    }
}
 
// Extension function for deleting all items in the CircularBuffer
extends mutates fun deleteAll(self: CircularBuffer) {
    self.map = emptyMap();
    self.length = 0;
    self.start = 0;
}
 
// Global static function for creating an empty CircularBuffer
fun emptyCircularBuffer(): CircularBuffer {
    return CircularBuffer{map: emptyMap()}; // length and start default to 0
}
 
// This contract records the last 5 timestamps of when "timer" message was received
contract MapAsCircularBuffer with Deployable {
    // Persistent state variables
    cBuf: CircularBuffer; // our circular buffer, which uses a map
 
    // Constructor (initialization) function of the contract
    init() {
        self.cBuf = emptyCircularBuffer();
    }
 
    // Internal message receiver, which responds to a String message "timer"
    // and records the timestamp when it receives such message
    receive("timer") {
        let timestamp: Int = now();
        self.cBuf.put(timestamp);
    }
 
    // Internal message receiver, which responds to a String message "get_first"
    // and replies with the first item of the circular buffer
    receive("get_first") {
        self.reply(self.cBuf.getIdx(0).toCoinsString().asComment());
    }
 
    // Internal message receiver, which responds to a String message "print_all"
    receive("print_all") {
        self.cBuf.printAll();
    }
 
    // Internal message receiver, which responds to a String message "delete_all"
    receive("delete_all") {
        self.cBuf.deleteAll();
    }
}
```

💡

This example is adapted from [Arrays page in Tact-By-Example (opens in a new tab)](https://tact-by-example.org/04-arrays).

🤔

Didn't find your favorite example of working with data structures? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/algo -->
# Algorithms – TactGitHub
Tact Language Documentation
Cookbook

Algorithms

# Algorithms

Algorithm is a finite sequence of rigorous instructions, typically used to solve a class of specific problems or to perform a computation.

🚨

This page is a stub. [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/time -->
# Time and date – TactGitHub
Tact Language Documentation
Cookbook

Time and date

# Time and date

## How to get the current time[](#how-to-get-the-current-time)

Use the `now()` method to obtain the current standard [Unix time (opens in a new tab)](https://en.wikipedia.org/wiki/Unix%5Ftime).

If you need to store the time in state or encode it in a message, use the following [serialization](/book/integers#serialization): `Int as uint32`.

```
let currentTime: Int = now();
 
if (currentTime > 1672080143) {
    // do something
}
```

💡

**Useful links:**  
[now() in Language→Reference](/language/ref/common#now)  
["Current Time" in Tact-By-Example (opens in a new tab)](https://tact-by-example.org/04-current-time)

🤔

Didn't find your favorite example of working with time and date? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/access -->
# Access control – TactGitHub
Tact Language Documentation
Cookbook

Access control

# Access control

This page lists common examples of working with privileges, ownership and access control.

## How to check sender privileges using Ownable trait[](#how-to-check-sender-privileges-using-ownable-trait)

```
// Ownable has to be imported from stdlib, as well as Deployable, for convenience:
import "@stdlib/ownable";
import "@stdlib/deploy";
 
message FooBarMsg {
    newVal: Int as uint32;
}
 
// Ownable trait can limit certain actions to the owner only
contract SenderChecker with Deployable, Ownable {
    // Persistent state variables
    owner: Address;     // Ownable trait requires you to add this exact state variable
    val: Int as uint32; // some value
 
    init() {
        // we can initialize owner to any value we want, the deployer in this case:
        self.owner = sender();
        self.val = 0;
    }
 
    receive("inc") {
        require(self.owner == sender(), "Only the owner can increase the value!");
        self.val += 1;
    }
 
    receive(msg: FooBarMsg) {
        require(self.owner == sender(), "Only the owner can set the value!");
        self.val = msg.newVal;
    }
}
```

🤔

Didn't find your favorite example of access control? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/random -->
# Randomness – TactGitHub
Tact Language Documentation
Cookbook

Randomness

# Randomness

This page lists examples of working with random numbers, uncertainty and randomness in general.

## How to generate a random number[](#how-to-generate-a-random-number)

```
// Declare a variable to store the random number
let number: Int;
 
// Generate a new random number, which is an unsigned 256-bit integer
number = randomInt();
 
// Generate a random number between 1 and 12
number = random(1, 12);
```

💡

**Useful links:**  
[randomInt() in Language→Reference](/language/ref/random#randomInt)  
[random() in Language→Reference](/language/ref/random#random)

🤔

Didn't find your favorite example of working with randomness? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/misc -->
# Miscellaneous – TactGitHub
Tact Language Documentation
Cookbook

Miscellaneous

# Miscellaneous

Various niche examples which don't yet have a dedicated page, but are useful and interesting nonetheless.

## How to throw errors[](#how-to-throw-errors)

The `throw()` function in a contract is useful when we don't know how often to perform a specific action.

It allows intentional exception or error handling, which leads to the termination of the current transaction and reverts any state changes made during that transaction.

```
let number: Int = 198;
 
// the error will be triggered anyway
throw(36);
 
// the error will be triggered only if the number is greater than 50
nativeThrowWhen(35, number > 50);
 
// the error will be triggered only if the number is NOT EQUAL to 198
nativeThrowUnless(39, number == 198);
```

💡

**Useful links:**  
[throw() in Language→Reference](/language/ref/advanced#throw)  
[Errors in Tact-By-Example (opens in a new tab)](https://tact-by-example.org/03-errors)

🤔

Didn't find your favorite example of working with something niche? Have cool implementations in mind? [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/multi-communication -->
# Multi-contract communication – TactGitHub
Tact Language Documentation
Cookbook

Multi-contract communication

# Multi-contract communication

🚨

This page is a stub. [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/jettons -->
# Fungible Tokens (Jettons) – TactGitHub
Tact Language Documentation
Cookbook

Fungible Tokens (Jettons)

# Fungible Tokens (Jettons)

🚨

This page is a stub. [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/nfts -->
# Non-Fungible Tokens (NFTs) – TactGitHub
Tact Language Documentation
Cookbook

Non-Fungible Tokens (NFTs)

# Non-Fungible Tokens (NFTs)

🚨

This page is a stub. [Contributions are welcome! (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues)
<!-- site: docs.tact-lang.org/cookbook/dexes/dedust -->
# DeDust.io – TactGitHub
Tact Language Documentation
Cookbook

Decentralized EXchanges (DEXes)

DeDust.io

# DeDust.io

[DeDust (opens in a new tab)](https://dedust.io) is a decentralized exchange (DEX) and automated market maker (AMM) built natively on [TON Blockchain (opens in a new tab)](https://ton.org) and [DeDust Protocol 2.0 (opens in a new tab)](https://docs.dedust.io/reference/tlb-schemes). DeDust is designed with a meticulous attention to user experience (UX), gas efficiency, and extensibility.

🚨

This page is a stub until it gets new content in [#146 (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues/146).
<!-- site: docs.tact-lang.org/cookbook/dexes/stonfi -->
# STON.fi – TactGitHub
Tact Language Documentation
Cookbook

Decentralized EXchanges (DEXes)

STON.fi

# STON.fi

[STON.fi (opens in a new tab)](https://ston.fi) is a decentralized automated market maker (AMM) built on [TON blockchain (opens in a new tab)](https://ton.org) providing virtually zero fees, low slippage, an extremely easy interface, and direct integration with TON wallets.

🚨

This page is a stub until it gets new content in [#149 (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues/149).
<!-- site: docs.tact-lang.org/book/types -->
# Type system overview – TactGitHub
Tact Language Documentation
Book

Type system overview

# Type system overview

Every variable, item, and value in Tact programs has a type. They can be:

* One of the [primitive types](/book/types#primitive-types)
* or [composite types](/book/types#composite-types)

Additionally, many of those types [can be made nullable](/book/types#optionals).

## Primitive types[](#primitive-types)

Tact supports a number of primitive data types that are tailored for smart contract use:

* [Int](/book/integers) — all numbers in Tact are 257257257\-bit signed integers, but [smaller representations](/book/integers#serialization) can be used to reduce storage costs.
* [Bool](/book/types#booleans) — classical boolean with `true` and `false` values.
* `Address` — standard [smart contract address (opens in a new tab)](https://docs.ton.org/learn/overviews/addresses#address-of-smart-contract) in TON Blockchain.
* `Slice`, `Cell`, `Builder` — low-level primitives of TON VM.
* `String` — represents text strings in TON VM.
* `StringBuilder` — helper type that allows you to concatenate strings in a gas-efficient way.

### Booleans[](#booleans)

The primitive type `Bool` is the classical boolean type, which can hold only the two values: `true` and `false`. It's convenient for boolean and logical operations, as well as for storing flags.

There are no implicit type conversions in Tact, so addition ([+](/book/operators#binary-add)) of two boolean values isn't possible. Hovewer, many comparison [operators](/book/statements#operators) are available, such as:

* `&&` for [logical AND](/book/operators#binary-logical-and),
* `||` for [logical OR](/book/operators#binary-logical-or),
* `!` for [logical inversion](/book/operators#unary-inverse),
* `==` and `!=` for checking [equality](/book/operators#binary-equality),
* and `!!` for [non-null assertion](/book/optionals).

Persisting bools to state is very space-efficient, as they only take 1-bit. Storing 1000 bools in state [costs (opens in a new tab)](https://ton.org/docs/develop/smart-contracts/fees#how-to-calculate-fees) about 0.000720.000720.00072 TON per year.

## Composite types[](#composite-types)

Using individual means of storage often becomes cumbersome, so there are ways to combine multiple [primitive types](/book/types#primitive-types) together to create composite types:

* [Maps](/book/types#maps) — associations of keys with values.
* [Structs and Messages](/book/types#structs-and-messages) — data structures with typed fields.
* [Optionals](/book/types#optionals) — `null` values for variables or fields of [Structs and Messages](/book/types#structs-and-messages).

Note, while [contracts](/book/types#contracts) and [traits](/book/types#traits) are also considered a part of the Tacts type system, one can't pass them around like [Structs and Messages](/book/types#structs-and-messages). Instead, it's possible to obtain the initial state of the given contract by using the [initOf](/book/expressions#initof) expression.

### Maps[](#maps)

The type [map<k, v\>](/book/maps) is used as a way to associate keys of type `k` with corresponding values of type `v`.

Example of a [map<k, v\>](/book/maps):

```
let mapExample: map<Int, Int> = emptyMap(); // empty map with Int keys and values
```

Learn more about them on a dedicated page: [Maps](/book/maps).

### Structs and Messages[](#structs-and-messages)

[Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) are two main ways of combining multiple [primitive types](/book/types#primitive-types) into a composite one.

Example of a [Struct](/book/structs-and-messages#structs):

```
struct Point {
    x: Int;
    y: Int;
}
```

Example of a [Message](/book/structs-and-messages#messages):

```
// Custom numeric id of the Message
message(0x11111111) SetValue {
    key: Int;
    value: Int?; // Optional, Int or null
    coins: Int as coins; // Serialization into TL-B types
}
```

Learn more about them on a dedicated page: [Structs and Messages](/book/structs-and-messages).

### Optionals[](#optionals)

All [primitive types](/book/types#primitive-types), as well as [Structs and Messages](/book/types#structs-and-messages) could be nullable and hold a special `null` value.

Example of an [optional](/book/optionals):

```
let opt: Int? = null; // Int or null, with explicitly assigned null
```

Learn more about them on a dedicated page: [Optionals](/book/optionals).

### Contracts[](#contracts)

[Contracts](/book/contracts) in Tact serve as the main entrypoints of smart contracts of TON blockchain. They hold all [functions](/book/functions), [getters](/book/functions#getter-functions), and [receivers](/book/functions#receiver-functions) of a TON contract, and much more.

Example of a [contract](/book/contracts):

```
contract HelloWorld {
    // Persistent state variable
    counter: Int;
 
    // Constructor function init(), where all the variables are initialized
    // Note, that an empty init function won't be needed starting with Tact 1.3.0,
    // see: https://github.com/tact-lang/tact/pull/167
    init() {
        self.counter = 0;
    }
 
    // Internal message receiver, which responds to a string message "increment"
    receive("increment") {
        self.counter += 1;
    }
 
    // Getter function with return type Int
    get fun counter(): Int {
        return self.counter;
    }
}
```

Read more about them on the dedicated page: [Contracts](/book/contracts).

### Traits[](#traits)

Tact doesn't support classical class inheritance, but instead introduces the concept of _traits_, which can be viewed as abstract contracts (like abstract classes in popular object-oriented languages). They have the same structure as [contracts](/book/types#contracts), but can't [initialize persistent state variables](/book/contracts#init-function), while allowing to override some of their behaviors.

Example of a trait [Ownable](/language/libs/ownable#ownable) from [@stdlib/ownable](/language/libs/ownable):

```
trait Ownable {
    // Persistent state variable, which cannot be initialized in the trait
    owner: Address;
 
    // Internal function
    fun requireOwner() {
        nativeThrowUnless(132, context().sender == self.owner);
    }
 
    // Getter function with return type Address
    get fun owner(): Address {
        return self.owner;
    }
}
```

And the [contract](/book/types#contracts) that uses trait [Ownable](/language/libs/ownable#ownable):

```
contract Treasure with Ownable {
    // persistent state variable, which MUST be defined in the contract
    owner: Address;
 
    // constructor function init(), where all the variables are initialized
    init(owner: Address) {
        self.owner = owner;
    }
}
```
<!-- site: docs.tact-lang.org/book/integers -->
# Integers – TactGitHub
Tact Language Documentation
Book

Integers

# Integers

Arithmetic in smart contracts on TON is always done with integers and never with floating-point numbers since the floats are [unpredictable (opens in a new tab)](https://learn.microsoft.com/en-us/cpp/build/why-floating-point-numbers-may-lose-precision). Therefore, the big accent goes on integers and their handling.

The only primitive number type in Tact is `Int`, for 257257257\-bit signed integers.  
It's capable of storing integers between −2256\-2^{256}−2256 and 2256−1.2^{256} - 1.2256−1.

## Notation[](#notation)

Tact supports various ways of writing primitive values of `Int` as [integer literals](/book/expressions#integer-literals).

Most of the notations allow adding underscores (`_`) in-between digits, except for:

* Representations in strings, as seen in [nano-tons](/book/integers#nano-tons) case.
* Decimal numbers written with a leading zero 0.0.0. Their use is generally discouraged, see [below](/book/integers#decimal).

Additionally, several underscores in a row as in 4\_\_24\\\_\\\_24\_\_2, or trailing underscores as in 42\_42\\\_42\_ are **not** allowed.

### Decimal[](#decimal)

Most common and most used way of representing numbers, using the [decimal numeral system (opens in a new tab)](https://en.wikipedia.org/wiki/Decimal): 123456789.123456789.123456789.  
You can use underscores (`_`) to improve readability: 123\_456\_789123\\\_456\\\_789123\_456\_789 is equal to 123456789.123456789.123456789.

⚠️

Alternatively, you can prefix the number with one 000, which prohibits use of underscores and only allows decimal digits: 0123\=123.0123 = 123.0123\=123.Note, that using this notation with leading zero is **strongly discouraged** due to possible confusion with octal integer literals in TypeScript, which is often used alongside Tact to develop and test contracts.

### Hexadecimal[](#hexadecimal)

Represent numbers using [hexadecimal numeral system (opens in a new tab)](https://en.wikipedia.org/wiki/Hexadecimal), denoted by the 0x\\mathrm{0x}0x (or 0X\\mathrm{0X}0X) prefix: 0xFFFFFFFFF.\\mathrm{0xFFFFFFFFF}.0xFFFFFFFFF.  
Use underscores (`_`) to improve readability: 0xFFF\_FFF\_FFF\\mathrm{0xFFF\\\_FFF\\\_FFF}0xFFF\_FFF\_FFF is equal to 0xFFFFFFFFF.\\mathrm{0xFFFFFFFFF}.0xFFFFFFFFF.

### Octal[](#octal)

Represent numbers using [octal numeral system (opens in a new tab)](https://en.wikipedia.org/wiki/Octal), denoted by the 0o\\mathrm{0o}0o (or 0O\\mathrm{0O}0O) prefix: 0o777777777.\\mathrm{0o777777777.}0o777777777.  
Use underscores (`_`) to improve readability: 0o777\_777\_777\\mathrm{0o777\\\_777\\\_777}0o777\_777\_777 is equal to 0o777777777.\\mathrm{0o777777777}.0o777777777.

### Binary[](#binary)

Represent numbers using [binary numeral system (opens in a new tab)](https://en.wikipedia.org/wiki/Binary%5Fnumber), denoted by the 0b\\mathrm{0b}0b (or 0B\\mathrm{0B}0B) prefix: 0b111111111.\\mathrm{0b111111111.}0b111111111.  
Use underscores (`_`) to improve readability: 0b111\_111\_111\\mathrm{0b111\\\_111\\\_111}0b111\_111\_111 is equal to 0b111111111.\\mathrm{0b111111111}.0b111111111.

### Nano-tons[](#nano-tons)

For example, arithmetic with dollars requires two decimal places after the dot — those are used for the cents value. But how would we represent the number $1.251.251.25 if we're only able to work with integers? The solution is to work with _cents_ directly. This way, $1.251.251.25 becomes 125125125 cents. We simply memorize that the two rightmost digits represent the numbers after the decimal point.

Similarly, working with Toncoins requires nine decimal places instead of the two. Therefore, the amount of 1.251.251.25 TON, which can be represented in Tact as [ton("1.25")](/language/ref/common#ton), is actually the number 125000000012500000001250000000. We refer to such numbers as _nano-tons_ (or _nanoToncoins_) rather than _cents_.

## Serialization[](#serialization)

When encoding `Int` values to persistent state (fields of [contracts](/book/contracts) and [traits](/book/types#traits)), it's usually better to use smaller representations than 257257257\-bits to reduce [storage costs (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/fees#storage-fee). Usage of such representations is also called "serialization" due to them representing the native [TL-B (opens in a new tab)](https://docs.ton.org/develop/data-formats/tl-b-languagehttps://docs.ton.org/develop/data-formats/tl-b-language) types which TON Blockchain operates on.

The persistent state size is specified in every declaration of a state variable after the `as` keyword:

```
contract SerializationExample {
    // persistent state variables
    oneByte: Int as int8 = 0; // ranges from -128 to 127 (takes 8 bit = 1 byte)
    twoBytes: Int as int16;   // ranges from -32,768 to 32,767 (takes 16 bit = 2 bytes)
 
    init() {
        // needs to be initialized in the init() because it doesn't have the default value
        self.twoBytes = 55*55;
    }
}
```

Integer serialization is also available for the fields of [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages), as well as in key/value types of [maps](/book/maps):

```
struct StSerialization {
    martin: Int as int8;
}
 
message MsgSerialization {
    seamus: Int as int8;
    mcFly: map<Int as int8, Int as int8>;
}
```

Motivation is very simple:

* Storing 100010001000 257257257\-bit integers in state [costs (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/fees#how-to-calculate-fees) about 0.1840.1840.184 TON per year.
* Storing 100010001000 323232\-bit integers only costs 0.0230.0230.023 TON per year by comparison.

### Serialization types[](#serialization-types)

| Name    | Inclusive range                                | Space taken                |
| ------- | ---------------------------------------------- | -------------------------- |
| uint8   | 000 to 28−12^{8} - 128−1                       | 8 bit = 1 byte             |
| uint16  | 000 to 216−12^{16} - 1216−1                    | 16 bit = 2 bytes           |
| uint32  | 000 to 232−12^{32} - 1232−1                    | 32 bit = 4 bytes           |
| uint64  | 000 to 264−12^{64} - 1264−1                    | 64 bit = 8 bytes           |
| uint128 | 000 to 2128−12^{128} - 12128−1                 | 128 bit = 16 bytes         |
| uint256 | 000 to 2256−12^{256} - 12256−1                 | 256 bit = 32 bytes         |
| int8    | −27\-2^{7}−27 to 27−12^{7} - 127−1             | 8 bit = 1 byte             |
| int16   | −215\-2^{15}−215 to 215−12^{15} - 1215−1       | 16 bit = 2 bytes           |
| int32   | −231\-2^{31}−231 to 231−12^{31} - 1231−1       | 32 bit = 4 bytes           |
| int64   | −263\-2^{63}−263 to 263−12^{63} - 1263−1       | 64 bit = 8 bytes           |
| int128  | −2127\-2^{127}−2127 to 2127−12^{127} - 12127−1 | 128 bit = 16 bytes         |
| int256  | −2255\-2^{255}−2255 to 2255−12^{255} - 12255−1 | 256 bit = 32 bytes         |
| int257  | −2256\-2^{256}−2256 to 2256−12^{256} - 12256−1 | 257 bit = 32 bytes + 1 bit |
| coins   | 000 to 2120−12^{120} - 12120−1                 | 120 bit = 15 bytes         |

💡

Read more on serialization here: [Compatibility with FunC](/book/func#convert-serialization)

## Operations[](#operations)

All runtime calculations with numbers are done at 257-bits, so [overflows (opens in a new tab)](https://en.wikipedia.org/wiki/Integer%5Foverflow) are quite rare. Nevertheless, if any math operation overflows, an exception will be thrown, and the transaction will fail. You could say that Tact's math is safe by default.

Note, that there is no problem with mixing variables of [different state sizes](/book/integers#serialization) in the same calculation. At runtime they are all the same type no matter what — 257257257\-bit signed, so overflows won't happen then.

However, this can still lead to **errors** in the [compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase) of the transaction. Consider the following example:

```
import "@stdlib/deploy";
 
contract ComputeErrorsOhNo with Deployable {
    oneByte: Int as uint8; // persistent state variable, max value is 255
 
    init() {
        self.oneByte = 255; // initial value is 255, everything fits
    }
 
    receive("lets break it") {
        let tmp: Int = self.oneByte * 256; // no runtime overflow
        self.oneByte = tmp; // whoops, tmp value is out of the expected range of oneByte
    }
}
```

Here, `oneByte` is serialized as a [uint8](/book/integers#serialization-types), which occupies only one byte and ranges from 000 to 28−12^{8} - 128−1, which is 255255255. And when used in runtime calculations no overflow happens and everything is calculated as a 257257257\-bit signed integers. But the very moment we decide to store the value of `tmp` back into `oneByte` we get an error with the [exit code 5](/book/exit-codes#5), which states the following: `Integer out of the expected range`.

⚠️

Therefore, be **very** careful with numbers and always double-check calculations when using serialization.
<!-- site: docs.tact-lang.org/book/maps -->
# Maps – TactGitHub
Tact Language Documentation
Book

Maps

# Maps

The [composite type](/book/types#composite-types) `map<k, v>` is used as a way to associate keys of type `k` with corresponding values of type `v`.

For example, `map<Int, Int>` uses [Int](/book/integers) type for its keys and values:

```
struct IntToInt {
    counters: map<Int, Int>;
}
```

## Allowed types[](#allowed-types)

Allowed key types:

* [Int](/book/integers)
* [Address](/book/types#primitive-types)

Allowed value types:

* [Int](/book/integers)
* [Bool](/book/types#booleans)
* [Cell](/book/types#primitive-types)
* [Address](/book/types#primitive-types)
* [Struct](/book/structs-and-messages#structs)
* [Message](/book/structs-and-messages#messages)

## Operations[](#operations)

### Declare[](#declare)

As a [local variable](/book/statements#let), using `emptyMap()` function of standard library:

```
let fizz: map<Int, Int> = emptyMap();
let fizz: map<Int, Int> = null; // identical to the previous line, but less descriptive
```

As a [persistent state variable](/book/contracts#variables):

```
contract Example {
    fizz: map<Int, Int>; // Int keys to Int values
    init() {
        self.fizz = emptyMap(); // redundant and can be removed!
    }
}
```

Note, that [persistent state variables](/book/contracts#variables) of type `map<k, v>` are initialized empty by default and don't need default values or an initialization in the [init() function](/book/contracts#init-function).

### Set values, `.set()`[](#set)

To set or replace the value under a key call the `.set()` [method](/book/functions#extension-function), which is accessible for all maps.

```
// Empty map
let fizz: map<Int, Int> = emptyMap();
 
// Setting a couple of values under different keys
fizz.set(7, 7);
fizz.set(42, 42);
 
// Overriding one of the existing key-value pairs
fizz.set(7, 68); // key 7 now points to value 68
```

### Get values, `.get()`[](#get)

To check if a key is found in the map by calling the `.get()` [method](/book/functions#extension-function), which is accessible for all maps. This will return `null` if the key is missing, or the value if the key is found.

```
// Empty map
let fizz: map<Int, Int> = emptyMap();
 
// Setting a value
fizz.set(68, 0);
 
// Getting the value by its key
let gotButUnsure: String? = fizz.get(68);          // returns String or null, therefore the type is String?
let mustHaveGotOrErrored: String = fizz.get(68)!!; // explicitly asserting that the value must not be null,
                                                   // which may crush at runtime if the value is, in fact, null
 
// Alternatively, we can check for the key in the if statement
if (gotButUnsure != null) {
    // Hooray, let's use !! without fear now and cast String? to String
    let definitelyGotIt: String = fizz.get(68)!!;
} else {
    // Do something else...
}
```

### Delete entries[](#del)

To delete a single key-value pair (single entry), simply assign the `null` value to the key when using the [.set()](/book/maps#set) [method](/book/functions#extension-function).

```
// Empty map
let fizz: map<Int, Int> = emptyMap();
 
// Setting a couple of values under different keys
fizz.set(7, 123);
fizz.set(42, 321);
 
// Deleting one of the keys
fizz.set(7, null); // the entry under key 7 is now deleted
```

To delete all the entries from the map, re-assign the map using the `emptyMap()` function:

```
// Empty map
let fizz: map<Int, Int> = emptyMap();
 
// Setting a couple of values under different keys
fizz.set(7, 123);
fizz.set(42, 321);
 
// Deleting all of the entries at once
fizz = emptyMap();
fizz = null; // identical to the previous line, but less descriptive
```

With this approach all previous entries of the map are completely discarded from the contract even if the map was declared as its persistent state variable. As a result, assigning maps to `emptyMap()` **does not** inflict any hidden or sudden [storage fees (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/fees#storage-fee).

### Convert to a `Cell`, `.asCell()`[](#convert)

Use `.asCell()` [method](/book/functions#extension-function) on maps to convert all their values to a [Cell](/book/types#primitive-types) type. Be mindful, that [Cell](/book/types#primitive-types) type is able to store up to 1023 bits, so converting larger maps to the Cell will result in error.

As an example, this method is useful for sending small maps directly in the body of the reply:

```
contract Example {
    // Persistent state variables
    fizz: map<Int, Int>; // our map
 
    // Constructor (initialization) function of the contract
    init() {
        // Setting a bunch of values
        self.fizz.set(0, 3);
        self.fizz.set(1, 14);
        self.fizz.set(2, 15);
        self.fizz.set(3, 926);
        self.fizz.set(4, 5_358_979_323_846);
    }
 
    // Internal message receiver, which responds to empty messages
    receive() {
        // Here we're converting the map to a Cell and making a reply with it
        self.reply(self.fizz.asCell());
    }
}
```

### Traverse over entries[](#traverse)

At the moment Tact doesn't have a special syntax for iterating over maps. However, it's possible to use maps as a simple arrays if you define a `map<Int, v>` with an [Int](/book/integers) type for the keys and keep track of the number of items in the separate variable:

```
contract Iteration {
    // Persistent state variables
    counter: Int as uint32;    // counter of map entries, serialized as a 32-bit unsigned
    record: map<Int, Address>; // Int to Address map
 
    // Constructor (initialization) function of the contract
    init() {
        self.counter = 0; // Setting the self.counter to 0
    }
 
    // Internal message receiver, which responds to a String message "Add"
    receive("Add") {
        // Get the Context Struct
        let ctx: Context = context();
        // Set the entry: counter Int as a key, ctx.sender Address as a value
        self.record.set(self.counter, ctx.sender);
        // Increase the counter
        self.counter += 1;
    }
 
    // Internal message receiver, which responds to a String message "Send"
    receive("Send") {
        // Loop until the value of self.counter (over all the self.record entries)
        let i: Int = 0; // declare usual i for loop iterations
        while (i < self.counter) {
           send(SendParameters{
                bounce: false,              // do not bounce back this message
                to: self.record.get(i)!!,   // set the sender address, knowing that key i exists in the map
                value: ton("0.0000001"),    // 100 nanoToncoins (nano-tons)
                mode: SendIgnoreErrors,     // send ignoring errors in transaction, if any
                body: "SENDING".asComment() // String "SENDING" converted to a Cell as a message body
            });
            i += 1; // don't forget to increase the i
        }
    }
 
    // Getter function for obtaining the value of self.record
    get fun map(): map<Int, Address> {
        return self.record;
    }
 
    // Getter function for obtaining the value of self.counter
    get fun counter(): Int {
        return self.counter;
    }
}
```

It's often useful to set an upper-bound restriction on such maps, so that you [don't hit the limits](/book/maps#limits-and-drawbacks).

⚠️

Note, that manually keeping track of number of items or checking the length of such map is very error-prone and generally discouraged. Instead, try to wrap your map into the [Struct](/book/structs-and-messages#structs) and define [extension functions](/book/functions#extension-function) on it. See example in the Cookbook: [How to emulate an array using a map wrapped in a Struct](/cookbook/data-structures#array).

💡

This example was adapted from [howardpen9/while-example-tact (opens in a new tab)](https://github.com/howardpen9/while-example-tact/blob/de5807fcd20dba5f6a3748d112511477fb22bfcc/contracts/awesome.tact#L19C10-L19C10).

See other examples of map usage in the Cookbook:  
[How to emulate a stack using a map wrapped in a Struct](/cookbook/data-structures#stack)  
[How to emulate a circular buffer using a map wrapped in a Struct](/cookbook/data-structures#circular-buffer)

## Serialization[](#serialization)

It's possible to do [integer serialization](/book/integers#serialization-types) of map keys, values or both to [preserve space and reduce storage costs](/book/integers#serialization):

```
struct SerializedMapInside {
    // Both keys and values here would be serialized as 8-bit unsigned integers,
    // thus preserving the space and reducing storage costs:
    countersButCompact: map<Int as uint8, Int as uint8>;
}
```

💡

Read about other serialization options: [Compatibility with FunC](/book/func#convert-serialization).

## Limits and drawbacks[](#limits-and-drawbacks)

While maps can be convenient to work with on a small scale, they cause a number of issues if the number of items is unbounded and map can significantly grow in size:

* As the upper bound of the smart contract state size is around 65 00065\\,00065000 items of type [Cell](/book/types#primitive-types), it constrains the storage limit of maps to be about 30 00030\\,00030000 key-value pairs for the whole contract.
* The more entries you have in a map, the bigger [compute fees (opens in a new tab)](https://docs.ton.org/develop/howto/fees-low-level#computation-fees) you'll get. Thus, working with large maps makes compute fees tough to predict and manage.
* Using a large map in a single contract doesn't allow to distribute its workload. Hence, it can make the overall performance much worse compared to using a smaller map and a bunch of interacting smart contracts.

To resolve such issues you can set an upper-bound restriction on a map as a constant and check against it every time you're setting a new value to the map:

```
contract Example {
    // Declare a compile-time constant upper-bound for our map
    const MaxMapSize: Int = 42;
 
    // Persistent state variables
    arr: map<Int, Int>; // "array" of String values as a map
    arrLength: Int = 0;    // length of the "array", defaults to 0
 
    // Constructor (initialization) function of the contract
    init() {}
 
    // Internal function for pushing an item to the end of the "array"
    fun arrPush(item: String) {
        if (self.arrLength >= self.MaxMapSize) {
            // Do something, stop the operation, for example
        } else {
            // Proceed with adding new item
            self.arr.set(self.arrLength, item);
            self.arrLength += 1;
        }
    }
}
```

If you still need a large map or an unbound (infinitely large) map, it's better to architect your smart contracts according to the [asynchronous and actor-based model of TON blockchain (opens in a new tab)](https://docs.ton.org/learn/overviews/ton-blockchain). That is, to use contract sharding and essentially make the whole blockchain a part of your map(s).
<!-- site: docs.tact-lang.org/book/structs-and-messages -->
# Structs and Messages – TactGitHub
Tact Language Documentation
Book

Structs and Messages

# Structs and Messages

Tact supports a number of [primitive data types](/book/types#primitive-types) that are tailored for smart contract use. However, using individual means of storage often becomes cumbersome, so there are [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) which allow combining types together.

⚠️

**Warning**: Currently circular types are **not** possible. This means that Struct/Message **A** can't have a field of a Struct/Message **B** that has a field of the Struct/Message **A**.

Therefore, the following code **won't** compile:

```
struct A {
    circularFieldA: B;
}
 
struct B {
    impossibleFieldB: A;
}
```

## Structs[](#structs)

Structs can define complex data types that contain multiple fields of different types. They can also be nested.

```
struct Point {
    x: Int as int64;
    y: Int as int64;
}
 
struct Line {
    start: Point;
    end: Point;
}
```

Structs can also include both default fields and optional fields. This can be quite useful when you have many fields but don't want to keep respecifying them.

```
struct Params {
    name: String = "Satoshi";  // default value
    age: Int?;                 // optional field
    point: Point;              // nested Structs
}
```

Structs are also useful as return values from getters or other internal functions. They effectively allow a single getter to return multiple return values.

```
contract StructsShowcase {
    params: Params; // Struct as a Contract persistent state variable
 
    init() {
        self.params = Params{point: Point{x: 4, y: 2}};
    }
 
    get fun params(): Params {
        return self.params;
    }
}
```

The order of fields does not matter. Unlike other languages, Tact does not have any padding between fields.

## Messages[](#messages)

Messages can hold [Structs](/book/structs-and-messages#structs) in them:

```
struct Point {
    x: Int;
    y: Int;
}
 
message Add {
    point: Point; // holds a struct Point
}
```

Messages are almost the same thing as [Structs](/book/structs-and-messages#structs) with the only difference that Messages have a 32-bit integer header in their serialization containing their unique numeric id. This allows Messages to be used with [receivers](/book/receive) since the contract can tell different types of messages apart based on this id.

Tact automatically generates those unique ids for every received Message, but this can be manually overwritten:

```
// This Message overwrites its unique id with 0x7362d09c
message(0x7362d09c) TokenNotification {
    forwardPayload: Slice as remaining;
}
```

This is useful for cases where you want to handle certain opcodes (operation codes) of a given smart contract, such as [Jetton standard (opens in a new tab)](https://github.com/ton-blockchain/TEPs/blob/master/text/0074-jettons-standard.md). The short-list of opcodes this contract is able to process is [given here in FunC (opens in a new tab)](https://github.com/ton-blockchain/token-contract/blob/main/ft/op-codes.fc). They serve as an interface to the smart contract.

💡

For more in-depth information on this see:  
[Convert received messages to op operations](/book/func#convert-received-messages-to-op-operations)  
[Internal message body layout in TON Docs (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/guidelines/internal-messages#internal-message-body)  
[Messages of the Jetton implementation in Tact (opens in a new tab)](https://github.com/howardpen9/jetton-implementation-in-tact/blob/9eee917877a92af218002874a9f2bd3f9c619229/sources/messages.tact)  
[Jetton Standard in Tact on Tact-by-Example (opens in a new tab)](https://tact-by-example.org/07-jetton-standard)
<!-- site: docs.tact-lang.org/book/optionals -->
# Optionals – TactGitHub
Tact Language Documentation
Book

Optionals

# Optionals

As it was mentioned in [type system overview](/book/types#optionals), all [primitive types](/book/types#primitive-types), [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) could be nullable. That is, they don't necessarily hold any value, aside from `null` — a special value, which represents the intentional absence of any other value.

[Variables](/book/statements#let) or fields of [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) that can hold `null` are called "optionals". They're useful to reduce state size when the variable isn't necesserily used.

You can make any variable or a field an optional by adding a question mark (`?`) after its type declaration. The only exceptions are [map<k, v\>](/book/maps) and [bounced<Msg\>](/book/bounced#bounced-messages-in-tact), where you can't make them, inner key/value type (in case of a map) or the inner [Message](/book/structs-and-messages#messages) (in case of a bounced) optional.

Optional variables or optional fields that are not defined hold the `null` value by default. You cannot access them without checking for `null` first. But if you're certain they are not `null` at a given moment, use the [non-null assertion operator !!](/book/operators#unary-non-null-assert) to access their value.

Trying to access the value of an optional variable or an optional field without using [!!](/book/operators#unary-non-null-assert) or without checking for `null` beforehand will result in a compilation error.

Example of optionals:

```
struct StOpt {
    opt: Int?; // Int or null
}
 
message MsOpt {
    opt: StOpt?; // Notice, how the struct StOpt is used in this definition
}
 
contract Optionals {
    opt: Int?;
    address: Address?;
 
    init(opt: Int?) { // optionals as parameters
        self.opt = opt;
        self.address = null; // explicit null value
    }
 
    receive(msg: MsOpt) {
        let opt: Int? = 12; // defining a new variable
        if (self.opt != null) { // explicit check
            self.opt = opt!!; // using !! as we know that opt value isn't null
        }
    }
}
```
<!-- site: docs.tact-lang.org/book/contracts -->
# Contracts – TactGitHub
Tact Language Documentation
Book

Contracts

# Contracts

Contracts in Tact are similar to classes in popular object-oriented languages, except that their instances are deployed on the blockchain and they can't be passed around like [Structs and Messages](/book/structs-and-messages).

## Self-references[](#self)

Contracts and [traits](/book/types#traits) have a built-in [identifier](/book/expressions#identifiers) `self`, which is used for referring to their fields (persistent state [variables](/book/contracts#variables) and [constants](/book/contracts#variables)) and methods ([internal functions](/book/contracts#internal-functions)):

```
contract Example {
    // persistent state variables
    foo: Int;
 
    init() {
        self.foo = 42; // <- referencing variable foo through self.
    }
}
```

## Structure[](#structure)

Each contract can contain:

* [Persistent state variables](/book/contracts#variables)
* [Constructor function init()](/book/contracts#init-function)
* [Contract constants](/book/contracts#constants)
* [Getter functions](/book/contracts#get-functions)
* [Receiver functions](/book/contracts#receiver-functions)
* [Internal functions](/book/contracts#internal-functions)

Furthermore, contracts can inherit all the declarations from [traits](/book/types#traits) and override some of their default behaviours.

### Persistent state variables[](#variables)

Contracts can define state variables that persist between contract calls. Contracts in TON [pay rent (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/fees#storage-fee) in proportion to the amount of persistent space they consume, so [compact representations via serialization](/book/integers#serialization) are encouraged.

```
contract Example {
    // persistent state variables
    val: Int;              // Int
    val32: Int as uint32;  // Int serialized to an 32-bit unsigned
    mapVal: map<Int, Int>; // Int keys to Int values
    optVal: Int?;          // Int or null
}
```

State variables must have a default value or initialized in [init()](/book/contracts#init-function) function, that runs on deployment of the contract. The only exception is persistent state variables of type [map<k, v\>](/book/maps) since they are initialized empty by default.

💡

Note, that Tact supports local, non-persistent-state variables too, see: [Variable declaration](/book/statements#let).

### Contract constants[](#constants)

Unlike [variables](/book/contracts#variables), constants cannot change. Their values are calculated in _compile-time_ and cannot change during execution.

There isn't much difference between constants defined outside of a contract (global constants) and inside the contract (contract constants). Those defined outside can be used by other contracts in your project.

Constant initializations must be relatively simple and only rely on values known during compilation. If you add two numbers for example, the compiler will calculate the result during build and put the result in your compiled code.

You can read constants both in [receivers](/book/contracts#receiver-functions) and in [getters](/book/contracts#getter-functions).

Unlike [contract variables](/book/contracts#variables), **contract constants don't consume space in persistent state**. Their values are stored directly in the code [Cell](/book/types#primitive-types) of the contract.

```
// global constants are calculared in compile-time and cannot change
const GlobalConst1: Int = 1000 + ton("42") + pow(10, 9);
 
contract Example {
    // contract constants are also calculated in compile-time and cannot change
    const ContractConst1: Int = 2000 + ton("43") pow(10, 9);
 
    // contract constants can be an easy alternative to enums
    const StateUnpaid: Int = 0;
    const StatePaid: Int = 1;
    const StateDelivered: Int = 2;
    const StateDisputed: Int = 3;
 
    // no need to init constants
    init() {}
 
    get fun sum(): Int {
        // access constants from anywhere
        return GlobalConst1 + self.ContractConst1 + self.StatePaid;
    }
}
```

Read more about constants on their dedicated page: [Constants](/book/constants).

### Constructor function `init()`[](#init-function)

On deployment of the contract, the constructor function `init()` is run.

If a contract has any [persistent state variables](/book/contracts#variables) without default values specified, it must initialize them in this function.

```
contract Example {
    // persistent state variables
    var1: Int = 0; // initialized with default value 0
    var2: Int;     // must be initialized in the init() function
 
    // constructor function
    init() {
        self.var2 = 42;
    }
}
```

💡

To obtain initial state of the target contract in [internal functions](/book/contracts#internal-functions), [receivers](/book/contracts#receiver-functions) or [getters](/book/contracts#getter-functions) use [initOf](/book/expressions#initof) expression.

### Getter functions[](#getter-functions)

[Getter functions](/book/functions#getter-functions) are **not accessible from other contracts and exported only to off-chain world**.

Additionally, **getters cannot modify the contract's state variables**, only read their values and use them in expressions.

```
contract HelloWorld {
    foo: Int;
 
    init() {
        self.foo = 0;
    }
 
    // getter function with return type Int
    get fun foo(): Int {
        return self.foo; // can't change self.foo here
    }
}
```

Read more about them in their dedicated section: [Getter functions](/book/functions#getter-functions)

### Receiver functions[](#receiver-functions)

[Receiver functions](/book/functions#receiver-functions) in Tact can be one of the following three kinds:

* [receive()](/book/receive), which receive internal messages (from other contracts).
* [bounced()](/book/bounced), which are called when outgoing message from this contract has bounced back.
* [external()](/book/external), which don't have a sender and can be sent by anyone in the world.

```
message CanBounce {
    counter: Int;
}
 
contract HelloWorld {
    counter: Int;
 
    init() {
        self.counter = 0;
    }
 
    get fun counter(): Int {
        return self.counter;
    }
 
    // internal message receiver, which responds to a string message "increment"
    receive("increment") {
        self.counter += 1;
 
        // sending the message back to the sender
        send(SendParameters{
            to: sender(),
            value: 0,
            mode: SendRemainingValue | SendIgnoreErrors,
            body: CanBounce{counter: self.counter}.toCell()
        });
    }
 
    // bounced message receiver, which is called when the message bounces back to this contract
    bounced(src: bounced<MsBounced>) {
        self.counter = 0; // reset the counter in case message bounced
    }
 
    // external message receiver, which responds to off-chain message "hello, it's me"
    external("hello, it's me") {
        // can't be replied to as there's no sender!
        self.counter = 0;
    }
}
```

### Internal functions[](#internal-functions)

These functions behave similarly to private methods in popular object-oriented languages — they're internal to contracts and can be called by prefixing them with a special [identifier self](/book/contracts#field-access). That's why internal functions can sometimes be referred to as "contract methods".

Internal functions can access the contract's [persistent state variables](/book/contracts#variables) and [constants](/book/contracts#constants).

They can only be called from [receivers](/book/contracts#receiver-functions), [getters](/book/contracts#getter-functions) and other internal functions, but not from other contracts or [init()](/book/contracts#init-function).

```
contract Functions {
    val: Int = 0;
    init() {}
 
    // this contract method can only be called from within this contract and access its variables
    fun onlyZeros() {
        require(self.val == 0, "Only zeros are permitted!");
    }
 
    // receiver function, which calls the internal function onlyZeros
    receive("only zeros") {
        self.onlyZeros();
    }
}
```

💡

Note, that Tact supports other kinds of functions too, see: [Functions](/book/functions).
<!-- site: docs.tact-lang.org/book/operators -->
# Operators – TactGitHub
Tact Language Documentation
Book

Operators

# Operators

Almost every contract operates on data: transforms some values into another. Scope may vary, but operators lay in core of such modifications.

This page lists all the operators in Tact in decreasing order of their [precedence](/book/operators#precedence), with examples of usage.

💡

Note, that there are no implicit type conversions in Tact, so operators can't be used to, say, add values of different type or compare them in terms of equality without explicitly casting to the same type. That's done with certain functions from the standard library. See [Int.toString()](/language/ref/strings#inttostring) for an example of such function.

## Precedence[](#precedence)

All operators on this page are given in order of decreasing precedence, from highest to lowest. Precedence is used to choose which operator would be considered in a particular situation. Whenever any ambiguity arises, Tact would prefer operators with higher precedence over those with lower.

For example, minus sign (`-`) may be considered as a subtraction operator or as a negation operator, which reverses the sign of the expression from plus to minus, or vice-versa. As the latter has the higher precedence over the former in cases of ambiguity between the two Tact will first consider `-` as a negation operator. And if that doesn't make sense for the given expression, only then it would consider it as a subtraction operator.

Consider the following code:

```
5 + -5; // here, the minus sign would be viewed as a negation operator
5 -5;   // while here it would be viewed as a subtraction operator, despite formatting
```

Even though this example may be simple, neglection of precedence rules can often lead to confusing situations with operators. The correct order of operations can be ensured by wrapping every operation in [parentheses](/book/operators#parentheses), since parentheses have the highest precedence of all expressions and operators there is.

## Parentheses, `()`[](#parentheses-)

Parentheses (also can be called round brackets, `()`) are more of a punctuation symbols than actual operators, but their [precedence](/book/operators#precedence) is higher than precedence of any other operator. Use parentheses to override order of operations:

```
5 * 5 - 2;   // 23
5 * (5 - 2); // 15
```

## Unary[](#unary)

Unary here means that they are applied only to one operand of the given expression. All unary operators, except for the [non-null assertion](/book/operators#unary-non-null-assert), are of the same [precedence](/book/operators#precedence).

Unary operators can be one of the two types:

* prefix — placed before the expression.
* postfix (or suffix) — placed after the expression.

### Non-null assert, `!!`[](#unary-non-null-assert)

Unary double-exclamation mark (_non-null assertion_) operator `!!` is a postfix operator, which enforces non-null values and allows direct access to the value of the optional variable if it's not `null`. Raises a compilation error otherwise. Can be applied to any optional variable regardless of its non-null type.

💡

Read more about optional variables and fields here: [Optionals](/book/optionals)

### Plus, `+`[](#unary-plus)

Although unary plus sign operator `+` is specified in the grammar of Tact compiler, it only exists as a [binary operator](/book/operators#binary-plus).

### Negate, `-`[](#unary-negate)

Unary minus sign (_negation_) operator `-` is a prefix operator, which reverses the sign of the expression. Can only be applied to values of type [Int](/book/integers):

```
let five: Int = 5;
five + -five; // here, the minus sign is a negation operator, not a subtraction operator
-(-1);        // double application gives back the original value, which is 1
--1;          // SYNTAX ERROR!
```

### Inverse, `!`[](#unary-inverse)

Unary exclamation mark (_inversion_) operator `!` is a prefix operator, which inverts the boolean value of the expression — changes `true` to `false`, and vice versa. Can only be applied to values of type [Bool](/book/types#booleans):

```
let iLikeTact: Bool = true;
!iLikeTact; // false
!false;     // true
!!true;     // SYNTAX ERROR!
!(!true);   // true
```

## Binary[](#binary)

Binary operators are split into several subsections, in order of decreasing [precedence](/book/operators#precedence). Operators within each subsection have the same [precedence](/book/operators#precedence) as the subsection itself.

### Multiplication[](#binary-multiplication)

Multiply, divide or obtain a remainder.

#### Multiply, `*`[](#binary-multiply)

Binary asterisk (_multiplication_) operator `*` is used for multiplication of two values. Can cause [integer overflows](/book/integers#operations).

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two * two;         // 4
0 * 1_000_000_000; // 0
-1 * 5;            // -5
 
pow(2, 255) * pow(2, 255); // build error: integer overflow!
```

#### Divide, `/`[](#binary-divide)

Binary slash (_division_) operator `/` is used for integer division of two values, which truncates towards zero. An attempt to divide by zero would result in an error with [exit code 4](/book/exit-codes#4): `Integer overflow`.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two / 2; // 1
two / 1; // 0
-1 / 5;  // -1
```

#### Modulo, `%`[](#binary-modulo)

Binary percent sign (_modulo_) operator `%` is used for getting the modulo of an integer division, which must not be confused with getting a remainder. For two values of the same sign, modulo and remainder operations are equivalent, but when the operands are of different signs, the modulo result always has the same sign as the _divisor_ (value on the right), while the remainder has the same sign as the _dividend_ (value on the left), which can make them differ by one unit of the _divisor_.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two % 2; // 0
two % 1; // 1
 
1 % 5;   // 1
-1 % 5;  // 4
1 % -5;  // -4
-1 % -5; // -1
```

The simplest way to avoid confusion between the two is to prefer using positive values via [abs(x: Int)](/language/ref/math#abs):

```
abs(-1) % abs(-5); // 1
```

💡

Did you know, that in JavaScript `%` works as a _remainder_ operator, but not _modulo_ operator (like in Tact)?  
[Remainder (%) - JavaScript (opens in a new tab)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Remainder#description)  
[Modulo - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Modulo)

### Addition[](#binary-addition)

Add or subtract.

#### Add, `+`[](#binary-add)

Binary plus (_addition_) operator `+` is used for adding numbers together. Going beyond the maximum value of an [Int](/book/integers) will result in an error with [exit code 4](/book/exit-codes#4): `Integer overflow`.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two + 2; // 4
-1 + 1;  // 0
 
pow(2, 254) + pow(2, 254);     // 2 * 2^254
pow(2, 255) + pow(2, 255);     // build error: integer overflow!
pow(2, 255) - 1 + pow(2, 255); // 2^256 - 1, maximal value of any integer in Tact!
```

#### Subtract, `-`[](#binary-subtract)

Binary minus (_subtraction_) operator `-` is used for subtracting numbers from each other. Going beyond the minimum value of an [Int](/book/integers) will result in an error with [exit code 4](/book/exit-codes#4): `Integer overflow`.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two - 2; // 0
-1 - 1;  // -2
 
pow(2, 254) - pow(2, 254); // 0
pow(2, 255) - pow(2, 255); // 0
pow(2, 256) - pow(2, 256); // build error: integer overflow!
```

### Bitwise[](#binary-bitwise)

Manipulate individual bits.

#### Shift right, `>>`[](#binary-bitwise-shift-right)

Binary double greater than (_bitwise shift right_) operator `>>` returns an integer which binary representation is the _left operand_ value shifted by the _right operand_ number of bits to the right. Excess bits shifted off to the right are discarded, and copies of the leftmost bit are shifted in from the left. This operation is also called "sign-propagating right shift" or "arithmetic right shift", because the sign of the resulting number is the same as the sign of the _left operand_. This is a more effective way to divide the _left operand_ by 2n2^n2n, where nnn is equal to the _right operand_.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two >> 1; // 1
4 >> 1;   // 2
5 >> 1;   // 2, due to flooring of integer values
 
pow(2, 254) >> 254; // 1
```

💡

[Bit shifts - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#Bit%5Fshifts)  
[Bit manipulation - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bit%5Fmanipulation)

#### Shift left, `<<`[](#binary-bitwise-shift-left)

Binary double greater than (_bitwise shift right_) operator `<<` returns an integer which binary representation is the _left operand_ value shifted by the _right operand_ number of bits to the left. Excess bits shifted off to the left are discarded, and zero bits are shifted in from the right. This is a more effective way to multiply the _left operand_ by 2n2^n2n, where nnn is equal to the _right operand_. Going beyond the maximum value of an [Int](/book/integers) will result in an error with [exit code 4](/book/exit-codes#4): `Integer overflow`.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two << 1; // 4
1 << 5;   // 1 * 2^5, which is 32
2 << 5;   // 2 * 2^5, which is 64
 
pow(2, 254) == (1 << 254); // true
pow(2, 254) == 1 << 254; // true, no parentheses needed due to higher precedence of >> over ==
pow(2, 255) == 1 << 255; // true, but we're very close to overflow here!
```

💡

[Bit shifts - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#Bit%5Fshifts)  
[Bit manipulation - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bit%5Fmanipulation)

#### Bitwise AND, `&`[](#binary-bitwise-and)

Binary ampersand (_bitwise and_) operator `&` applies a [bitwise AND (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#AND), which performs the [logical AND](/book/operators#binary-logical-and) operation on each pair of the corresponding bits of operands. This is useful when we want to clear selected bits off a number, where each bit represents an individual flag or a boolean state, which makes it possible to "store" up to 257257257 boolean values per integer, as all integers in Tact are 257257257\-bit signed.

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two & 1;          // 0
4 & 1;            // 0
3 & 1;            // 1
1 & 1;            // 1
255 & 0b00001111; // 15
```

💡

[Bitwise AND - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#AND)  
[Bit manipulation - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bit%5Fmanipulation)

#### Bitwise OR, `|`[](#binary-bitwise-or)

Binary bar (_bitwise or_) operator `|` applies a [bitwise OR (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#OR), which performs the [logical OR](/book/operators#binary-logical-or) operation on each pair of the corresponding bits of operands. This is useful when we want to apply a specific [bitmask (opens in a new tab)](https://en.wikipedia.org/wiki/Mask%5F%28computing%29).

Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two | 1; // 3
4 | 1;   // 5
3 | 1;   // 3
1 | 1;   // 1
 
255 | 0b00001111;        // 255
0b11110000 | 0b00001111; // 255
```

💡

[Bitwise OR - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bitwise%5Foperation#OR)  
[Bit manipulation - Wikipedia (opens in a new tab)](https://en.wikipedia.org/wiki/Bit%5Fmanipulation)

### Comparison[](#binary-comparison)

Perform inequality and equality checks, find bigger, smaller or equal values.

#### Equal and not equal, `==` `!=`[](#binary-equality)

Binary equality (_equal_) operator `==` checks whether its two operands are _equal_, returning a result of type [Bool](/book/types#booleans).

Binary inequality (_not equal_) operator `!=` checks whether its two operands are _not equal_, returning a result of type [Bool](/book/types#booleans).

Both operators require operands to be of the same type and both don't perform implicit type conversions, except for the [Cell](/book/types#primitive-types) and [Slice](/book/types#primitive-types) types, which are implicitly compared by their hashes.

Both operators can be applied to the following list of types and values:

* [Int](/book/integers)
* [Bool](/book/types#booleans)
* [Address](/book/types#primitive-types)
* [Cell](/book/types#primitive-types), implicitly compares via `.hash()`
* [Slice](/book/types#primitive-types), implicitly compares via `.hash()`
* [String](/book/types#primitive-types)
* [map<k, v\>](/book/maps), but only if their key and value types are identical
* [Optionals and null value](/book/optionals)

```
// Int:
2 == 3; // false
2 != 3; // true
 
// Bool:
true == true;  // true
false != true; // true
 
// Address:
myAddress() == myAddress(); // true
myAddress() != myAddress(); // false
 
// Cell:
emptyCell() == emptyCell(); // true
emptyCell() != emptyCell(); // false
 
// Slice:
"A".asSlice() == "A".asSlice(); // true
"A".asSlice() != "A".asSlice(); // false
 
// String:
"A" == "A"; // true
"A" != "A"; // false
 
// map<k, v>:
let map1: map<Int, Int> = emptyMap();
let map2: map<Int, Int> = emptyMap();
map1 == map2; // true
map1 != map2; // false
 
// Optionals and null values themselves
let nullable: Int? = null;
nullable == null; // true
null == null;     // true
nullable != null; // false
null != null;     // false
 
let anotherNullable: Int? = 5;
nullable == anotherNullable; // false
nullable != anotherNullable; // true
```

#### Greater than, `>`[](#binary-greater)

Binary _greater than_ operator `>` returns `true` if the left operand is greater than the right operand, and `false` otherwise. Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two > 2; // false
-1 > -3; // true
```

#### Greater than or equal to, `>=`[](#binary-greater-equal)

Binary _greater than or equal to_ operator `>=` returns `true` if the left operand is greater than or to the right operand, and `false` otherwise. Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two >= 2; // true
-1 >= -3; // true
```

#### Less than, `<`[](#binary-less)

Binary _less than_ operator `<` returns `true` if the left operand is less than the right operand, and `false` otherwise. Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two < 2; // false
-1 < -3; // false
```

#### Less than or equal to, `<=`[](#binary-less-equal)

Binary _less than or equal to_ operator `<=` returns `true` if the left operand is less than or equal to the right operand, and `false` otherwise. Can only be applied to values of type [Int](/book/integers):

```
let two: Int = 2;
two <= 2; // true
-1 <= -3; // false
```

### Logical AND, `&&`[](#binary-logical-and)

Binary logical AND ([logical conjunction (opens in a new tab)](https://en.wikipedia.org/wiki/Logical%5Fconjunction)) operator `&&` returns `true` if both operands are `true`, and `false` otherwise. It's short-circuited, meaning that it would immediately evaluate the whole expression as `false` if the left operand is `false`, without evaluating the right one.

Can only be applied to values of type [Bool](/book/types#booleans):

```
let iLikeTact: Bool = true;
iLikeTact && true;  // true
iLikeTact && false; // false, evaluated both operands
false && iLikeTact; // false, didn't evaluate iLikeTact
```

### Logical OR, `||`[](#binary-logical-or)

Binary logical OR ([logical disjunction (opens in a new tab)](https://en.wikipedia.org/wiki/Logical%5Fdisjunction)) operator `||` returns `false` only if both operands are `false`, and `true` otherwise. It's short-circuited, meaning that it would immediately evaluate the whole expression as `true` if the left operand is `true`, without evaluating the right one.

Can only be applied to values of type [Bool](/book/types#booleans):

```
let iLikeSnails: Bool = false;
iLikeSnails && true;  // true
iLikeSnails && false; // false, evaluated both operands
true && iLikeSnails;  // true, didn't evaluate iLikeSnails
```

## Ternary, `?:`[](#ternary)

Conditional (_ternary_) operator is the only Tact operator that takes three operands: a condition followed by a question mark (`?`), then an expression to execute if the condition is evaluated to `true` followed by a colon (`:`), and finally the expression to execute if the condition is evaluated to `false`. This operator is frequently used as an alternative to an [if...else](/book/statements#if-else) statement.

Condition must resolve to type [Bool](/book/types#booleans):

```
// condition
// ↓
true ? "incredibly so" : "absolutely not"; // "incredibly so"
//     ---------------   ----------------
//     ↑                 ↑
//     |                 alternative, when condition is false
//     |
//     consequence, when condition is true
 
2 + 2 == 4 ? true : false; // true
```

Ternary operator is the only operator with right associativity, besides [assignment-related ones](/book/operators#assignment). This means that in ambiguous situations Tact would prefer the longest matching sequence. In short, this makes bracket-less nesting of ternary operators possible, but only for alternative cases (the part that comes after the colon sign `:`):

```
// don't need additional parentheses for alternative cases
false ? 1 : (false ? 2 : 3); // 3
false ? 1 : false ? 2 : 3;   // also 3
false ? 1 : true ? 2 : 3;    // 2
 
// need additional parentheses for consequence cases (parts in-between ? and :)
false ? (false ? 1 : 2) : 3; // 3
false ? false ? 1 : 2 : 3;   // SYNTAX ERROR!
true ? (false ? 1 : 2) : 3;  // 2
```

## Assignment, `=`[](#assignment)

Assignment operator `=` is used to assign a value to a variable, or to a property of a [Message](/book/structs-and-messages#messages) or a [Struct](/book/structs-and-messages#structs). The assignment is a statement and it doesn't return a value.

```
let someVar: Int = 5;    // assignment operator = is used here...
someVar = 4;             // ...and here
someVar = (someVar = 5); // SYNTAX ERROR!
```

### Augmented assignment[](#augmented-assignment)

Augmented (or compound) assignment operators such as `+=` combine an operation with an [assignment](/book/operators#assignment). The augmented assignment is a statement and it doesn't return a value.

Augmented assignments are semantically equivalent to regular assignments, but with an operation:

```
let value: Int = 5;
 
// this:
value += 5;
// is equivalent to this:
value = value + 5;
```

List of augmented assignment operators:

* `+=`, which uses [addition operator +](/book/operators#binary-add). Can only be applied to values of type [Int](/book/integers).
* `-=`, which uses [subtraction operator \-](/book/operators#binary-subtract). Can only be applied to values of type [Int](/book/integers).
* `*=`, which uses [multiplication operator \*](/book/operators#binary-multiply). Can only be applied to values of type [Int](/book/integers).
* `/=`, which uses [division operator /](/book/operators#binary-divide). Can only be applied to values of type [Int](/book/integers).
* `%=`, which uses [modulo operator %](/book/operators#binary-modulo). Can only be applied to values of type [Int](/book/integers).

```
let value: Int = 5;
 
// +=
value + 5;         // adds 5
value = value + 5; // adds 5 and assigns result back
value += value;    // also adds 5 and assigns result back
 
// -=
value - 5;         // subtracts 5
value = value - 5; // subtracts 5 and assigns result back
value -= value;    // also subtracts 5 and assigns result back
 
// *=
value * 5;         // multiplies by 5
value = value * 5; // multiplies by 5 and assigns result back
value *= value;    // also multiplies by 5 and assigns result back
 
// /=
value / 5;         // divides by 5
value = value / 5; // divides by 5 and assigns result back
value /= value;    // also divides by 5 and assigns result back
 
// %=
value % 5;         // gets modulo by 5
value = value % 5; // gets modulo by 5 and assigns result back
value %= value;    // also gets modulo by 5 and assigns result back
```
<!-- site: docs.tact-lang.org/book/expressions -->
# Expressions – TactGitHub
Tact Language Documentation
Book

Expressions

# Expressions

Every operator in Tact forms an expression, but there's much more to uncover as Tact offers a wide range of expressive options to choose from.

## Literals[](#literals)

Literals represent values in Tact. These are fixed values—not variables—that you _literally_ provide in your code. All literals in Tact are expressions themselves.

You can also call [extension functions](/book/functions#extension-function) defined on certain [primitive types](/book/types#primitive-types) corresponding to literals right on the literal values:

```
// Calling toString() defined for Int on a integer literal:
42.toString();
 
// Calling asComment() defined for String on a string literal:
"Tact is awesome!".asComment();
```

### Integer literals[](#integer-literals)

Integer literals can be written in [decimal](/book/integers#decimal) (base 101010), [hexadecimal](/book/integers#hexadecimal) (base 161616), [octal](/book/integers#octal) (base 888) and [binary](/book/integers#binary) (base 222) notations:

* A [_decimal_ integer](/book/integers#decimal) literal is a sequence of digits (0−9\\mathrm{0 - 9}0−9).
* A leading 0x\\mathrm{0x}0x (or 0X\\mathrm{0X}0X) indicates a [hexadecimal integer](/book/integers#hexadecimal) literal. They can include digits (0−9\\mathrm{0 - 9}0−9) and the letters a−f\\mathrm{a - f}a−f and A−F\\mathrm{A - F}A−F. Note, that the case of a character does not change its value. Therefore: 0xa\\mathrm{0xa}0xa \= 0xA\\mathrm{0xA}0xA \= 101010 and 0xf\\mathrm{0xf}0xf \= 0xF\\mathrm{0xF}0xF \= 151515.
* A leading 0o\\mathrm{0o}0o (or 0O\\mathrm{0O}0O) indicates a [octal integer](/book/integers#octal) literals. They can include only the digits 0−7\\mathrm{0 - 7}0−7.
* A leading 0b\\mathrm{0b}0b (or 0B\\mathrm{0B}0B) indicates a [binary integer](/book/integers#binary) literal. THey can only include the digits 000 and 111.

⚠️

Be wary that in Tact integer literals with a leading 000 are still considered decimals, unlike in JavaScript/TypeScript where leading 000 indicates an octal!

Some examples of integer literals:

```
// decimal, base 10:
0, 42, 1_000, 020
 
// hexadecimal, base 16:
0xABC, 0xF, 0x0011
 
// octal, base 8:
0o777, 0o001
 
// binary, base 2:
0b01111001_01101111_01110101_00100000_01100001_01110010_01100101_00100000_01100001_01110111_01100101_01110011_01101111_01101101_01100101
```

Read more about integers and [Int](/book/integers) type on the dedicated page: [Integers](/book/integers).

### Boolean literals[](#boolean-literals)

The [Bool](/book/types#booleans) type has only two literal values: `true` and `false`.

```
true == true;
true != false;
```

Read more about booleans and [Bool](/book/types#booleans) type in the dedicated chapter: [Booleans](/book/types#booleans).

### String literals[](#string-literals)

A string literal is zero or more characters enclosed in double (`"`) quotation marks. All string literals are objects of [String](/book/types#primitive-types) type.

```
"foo"
"1234"
 
// Note, that at the moment Tact strings can't have escape characters in them:
"line \n another"; // SYNTAX ERROR!, see: https://github.com/tact-lang/tact/issues/25
 
// This means, that double quotes inside strings are also prohibited:
"this \"can't be!\""; // SYNTAX ERROR!
```

Read more about strings and [String](/book/types#primitive-types) type there: [Primitive types](/book/types#primitive-types).

### `null` literal[](#null-literal)

The `null` value is written with a `null` literal. It's **not** an [identifier](/book/expressions#identifiers) and doesn't refer to any object. It's also **not** an instance of a [primitive type](/book/types#primitive-types). Instead, `null` represents a lack of identification and the intentional absence of any value.

```
let var: Int? = null; // variable, which can hold null value
var = 42;
if (var != null) {
    var!! + var!!;
}
```

Read more about working with `null` on the dedicated page: [Optionals](/book/optionals).

## Identifiers[](#identifiers)

An identifier is a sequence of characters in the code that _identifies_ a [variable](/book/statements#let), [constant](/book/constants), [map](/book/maps) and a [function](/book/functions), as well as a [Struct](/book/structs-and-messages#structs), [Message](/book/structs-and-messages#messages), [contract](/book/contracts), [trait](/book/types#traits), or their fields and methods. Identifiers are case-sensitive and not quoted.

In Tact, identifiers can contain latin lowercase letters (`a-z`), latin uppercase letters (`A-Z`), underscores (`_`) and digits (0−9\\mathrm{0 - 9}0−9), but may not start with a digit. An identifier differs from a [string](/book/expressions#string-literals) in that a string is data, while an identifier is part of the code.

Note, that when identifiers for [primitive types](/book/types#primitive-types) start with an uppercase letter. Used-defined [composite types](/book/types#composite-types), such as [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) also must be capitalized.

## Instantiation[](#instantiation)

You can create instances of the following types:

* [Structs](/book/structs-and-messages#structs)
* [Messages](/book/structs-and-messages#messages)

```
struct StExample {
    fieldInit: Int = 1;
    fieldUninit: Int;
}
 
fun example() {
    StExample{ fieldUninit: 2 };               // instance with default value of fieldInit
    StExample{ fieldInit: 0, fieldUninit: 2 }; // instance with both fields set
}
```

## Field access[](#field-access)

You can directly access fields of the following types:

* [Structs](/book/structs-and-messages#structs)
* [Messages](/book/structs-and-messages#messages)

```
struct StExample {
    fieldInit: Int = 1;
    fieldUninit: Int;
}
 
fun example(): Int {
    let struct: StExample = StExample{ fieldUninit: 2 }; // instantiation
 
    struct.fieldInit;          // access a field
    return struct.fieldUninit; // return field value from the function
}
```

## Extension function call[](#extension-function-call)

[Extension functions](/book/functions#extension-function) are defined only on specific types. They can be called similar to method calls in many other languages:

```
42.toString(); // toString() is a stdlib function that is defined on Int type
```

## Static function call[](#static-function-call)

Anywhere in the function body, a global [static function](/book/functions#global-static-functions) or an internal function of a [contract](/book/contracts) can be called:

```
contract ExampleContract {
    init() {}
    receive() {
        now(); // now() is a static function of stdlib
        let expiration: Int = now() + 1000; // operation and variable declaration
        expiration = self.answerQuestion(); // internal function
    }
    fun answerQuestion(): Int {
        return 42;
    }
}
```

## `initOf`[](#initof)

Expression `initOf` computes initial state (`StateInit`) of a [contract](/book/contracts):

```
//                     argument values for the init() function of the contract
//                     ↓   ↓
initOf ExampleContract(42, 100); // returns a Struct StateInit{}
//     ---------------
//     ↑
//     name of the contract
```

Where `StateInit` is a built-in [Struct](/book/structs-and-messages#structs), that consists of:

| Field | Type                                | Description                                                                                    |
| ----- | ----------------------------------- | ---------------------------------------------------------------------------------------------- |
| code  | [Cell](/book/types#primitive-types) | initial code of the [contract](/book/contracts) (the compiled bytecode)                        |
| data  | [Cell](/book/types#primitive-types) | initial data of the [contract](/book/contracts) (arguments of init() function of the contract) |
<!-- site: docs.tact-lang.org/book/statements -->
# Statements – TactGitHub
Tact Language Documentation
Book

Statements

# Statements

The following statements can appear anywhere in the [function](/book/functions) body.

## `let` statement[](#let)

The `let` statement allows local and [block](/book/statements#block)\-scoped variable declaration.

In Tact, declaring a variable always requires an initial value and an explicit type:

```
let value: Int = 123;           // declaration
let valueOptional: Int? = null; // Int or null
 
let valueMap: map<Int, Int> = emptyMap();
let valueMapWithSerialization: map<Int as uint8, Int as uint8> = emptyMap();
```

## `return` statement[](#return)

The `return` statement ends [function](/book/functions) execution and specifies a value to be returned to the [function](/book/functions) caller.

```
// Simple wrapper over stdlib function now()
fun getTimeFromNow(offset: Int): Int {
    return now() + offset;
}
```

## Block[](#block)

A block statement is used to group zero or more statements. The block is delimited by a pair of braces ("curly braces", `{}`) and contains a list of zero or more statements and declarations.

```
{ // <- start of the block
    // arbitrary statements:
    let value: Int = 2 + 2;
    dump(value);
} // <- end of the block
 
{ dump(2 + 2); } // a block with only one statement
```

## Expression[](#expression)

An expression statement is an expression used in a place where a statement is expected. The expression is evaluated and its result is discarded — therefore, it makes sense only for expressions that have side effects, such as executing a function or updating a variable.

```
dump(2 + 2); // stdlib function
```

## Assignment[](#assignment)

Assignment statements use an [assignment operator](/book/operators#assignment) (`=`) or [augmented assignment operators](/book/operators#augmented-assignment) (assignments combined with an operation):

```
let value: Int; // declaration
value = 5;      // assignment
value += 5;     // augmented assignment (one of the many, see below)
```

💡

Read more about assignment and augmented assignment in their dedicated section: [assignment operators](/book/operators#assignment).

## Branches[](#branches)

Control the flow of the code.

### `if...else`[](#if-else)

⚠️

Curly brackets (code blocks) are required!

When executing an `if...else` statement, first, the specified condition gets evaluated. If the resulting value is `true`, the `then` statement block gets executed. Otherwise, if the condition evaluates to `false`, the optional `else` statement block will be executed. If the `else` block is missing, nothing happens and execution continues further.

Regular `if`\-statement:

```
// condition
// ↓
if (true) { // consequence, when condition is true
    dump(2 + 2);
}
```

With `else` clause:

```
// condition
// ↓
if (2 + 2 == 4) {
    // consequence, when condition is true
    dump(true);
} else {
    // alternative, when condition is false
    dump(false);
}
```

With nested `if...else`:

```
// condition
// ↓
if (2 + 2 == 3) {
    // consequence, when condition is true
    dump("3?");
//        condition2
//        ↓
} else if (2 + 2 == 4) {
    // another consequence, when condition2 is true
    dump(true);
} else {
    // alternative, when both condition and condition2 are false
    dump(false);
}
```

💡

Tact also has a ternary expression `?:`, which is described earlier in the Book: [Ternary](/book/operators#ternary).

## Loops[](#loops)

Conditionally repeat certain blocks of code multiple times.

### `repeat`[](#repeat-loop)

The `repeat` loop executes a block of code a specified number of times. Number of repetitions must be a non-negative 323232\-bit [Int](/book/integers). Otherwise an error with the [exit code 5](/book/exit-codes#5), `Integer out of the expected range` would be thrown.

In the following example, code inside the loop will be executed 101010 times:

```
let twoPow: Int = 1;
repeat (10) {  // repeat exactly 10 times
    twoPow *= 2;
}
```

### `while`[](#while-loop)

While loop continues executing the block of code as long as the given condition is `true`.

In the following example, the value of `x` is decremented by 111 on each iteration, so the loop will run 101010 times:

```
let x: Int = 10;
while (x > 0) {
    x -= 1;
}
```

### `do...until`[](#do-until-loop)

The `do...until` loop is a post-test loop that executes the block of code at least once, and then continues to execute it until the given condition becomes `true`.

In the following example, the value of `x` is decremented by 111 on each iteration, so the loop will run 101010 times:

```
let x: Int = 10;
do {
    x -= 1;  // executes this code block at least once
} until (x <= 0);
```

💡

For more loop examples see: [Loops in Tact-By-Example (opens in a new tab)](https://tact-by-example.org/04-loops).
<!-- site: docs.tact-lang.org/book/constants -->
# Constants – TactGitHub
Tact Language Documentation
Book

Constants

# Constants

Constants in Tact could be a little bit more advanced than in popular languages: they could be virtual and abstract. Smart contracts often need to implement multiple traits and sometimes you need to configure some of them in compile time. Constructors in traits are prohibited due to their unpredicted behavior. So, we have to use constants and fields instead to pass values to them. It is the job of a main contract to implement values and constants for all traits.

## Simple constant[](#simple-constant)

Let's start with a simple constant. It is a value that is defined in compile time and cannot be changed. You can define a constant on the top level or inside a contract/trait. Let's define a constant on top level:

```
const MY_CONSTANT: Int = 42;
```

Similar for traits and contracts:

```
trait MyTrait {
    const MY_CONSTANT: Int = 42;
}
 
contract MyContract {
    const MY_CONSTANT: Int = 42;
}
```

## Virtual and abstract constants[](#virtual-and-abstract-constants)

Virtual constants are the constants that could be defined in a trait but changed in a contract. It is useful when you need to configure some of the traits in compile time. Let's define a virtual constant and an abstract one:

```
trait MyTrait {
    virtual const MY_FEE: Int = ton("1.0");
}
 
trait MyAbstractTrait {
    abstract const MY_DEVFEE: Int;
}
```

Now you can overwrite defaults in the contract:

```
contract MyContract with MyTrait, MyAbstractTrait {
    override const MY_FEE: Int = ton("0.5");
    override const MY_DEVFEE: Int = ton("1000");
}
```

This could be very useful to help a compiler to have some values in compile time, for example, you can enable and disable features without needing to change the code and not wasting gas.

```
trait Treasure {
    virtual const ENABLE_TIMELOCK: Bool = true;
 
    receive("Execute") {
        if (self.ENABLE_TIMELOCK) {
            //
            // This branch would be removed in compile time if ENABLE_TIMELOCK is false
            //
        }
    }
}
 
contract MyContract with Treasure {
    override const ENABLE_TIMELOCK: Bool = false;
}
 
```
<!-- site: docs.tact-lang.org/book/functions -->
# Functions and their types – TactGitHub
Tact Language Documentation
Book

Functions

# Functions and their types

Functions in Tact could be defined in different ways:

* Global static function
* Extension functions
* Mutable functions
* Native functions
* Receiver functions
* Getter functions

## Global static functions[](#global-static-functions)

You can define global function anywhere in your program:

```
fun pow(a: Int, c: Int): Int {
  let res: Int = 1;
  repeat(c) {
    res *= a;
  }
  return res;
}
```

## Extension function[](#extension-function)

Extension functions allow you to implement extensions for any possible type.

> **Warning**The name of the first argument MUST be named `self` and the type of this argument is the type you are extending.

```
extends fun pow(self: Int, c: Int) {
  let res: Int = 1;
  repeat(c) {
    res *= self;
  }
  return res;
}
```

## Mutable functions[](#mutable-functions)

Mutable functions are performing mutation of a value replacing it with an execution result. To perform mutation, the function must change the `self` value.

```
extends mutates fun pow(self: Int, c: Int) {
  let res: Int = 1;
  repeat(c) {
    res *= self;
  }
  self = res;
}
```

## Native functions[](#native-functions)

Native functions are direct bindings of func functions:

> **Note**Native functions could be also mutable and extension ones.

```
@name(store_uint)
native storeUint(s: Builder, value: Int, bits: Int): Builder;
 
@name(load_int)
extends mutates native loadInt(self: Slice, l: Int): Int;
```

## Receiver functions[](#receiver-functions)

Receiver functions are special functions that are responsible for receiving messages in contracts and could be defined only within a contract or trait.

```
contract Treasure {
 
  // ...
  
  // This means that this contract can receive the comment "Increment" and this function would be called for such messages
  receive("Increment") {
    self.counter += 1;
  }
}
```

## Getter Functions[](#getter-functions)

Getter functions define getters on smart contracts and can be defined only within a contract or trait.

```
contract Treasure {
 
  // ...
  
  get fun counter(): Int {
    return self.counter;
  }
}
```
<!-- site: docs.tact-lang.org/book/receive -->
# Receive messages – TactGitHub
Tact Language Documentation
Book

Receive messages

# Receive messages

TON is a distributed blockchain which means that communication between contracts is done by sending and receiving messages. The most common type of message is the internal message - a message sent from one contract (or a wallet) to another.

## Receive internal messages[](#receive-internal-messages)

To receive a message of the required type, you need to declare a receiver function, for example, `receive("increment")`. This notation means the declaration of a receiver function that will be called when a text with the value `"increment"` is sent to the contract. The function body can modify the state of the contract and send messages to other contracts. It is impossible to call a receiver directly. If you need to reuse some logic you can declare a function and call it from the receiver.

There are several receiver functions. All receiver functions are processed in the order they are listed below:

* `receive()` \- called when an empty message is sent to the contract
* `receive("message")` \- called when a text message with a specific comment is sent to the contract
* `receive(str: String)` \- called when an arbitrary text message is sent to the contract
* `receive(msg: MyMessage)` \- called when a binary message of type `MyMessage` is sent to the contract
* `receive(msg: Slice)` \- called when binary message of unknown type is sent to the contract

```
message MyMessage {
    value: Int;
}
 
contract MyContract {
    receive() {
        // ...
    }
    receive("message") {
        // ...
    }
    receive(str: String) {
        // ...
    }
    receive(msg: MyMessage) {
        // ...
    }
    receive(msg: Slice) {
        // ...
    }
}
```
<!-- site: docs.tact-lang.org/book/bounced -->
# Bounced messages – TactGitHub
Tact Language Documentation
Book

Bounced messages

# Bounced messages

When a contract sends a message with a flag bounce set to true, then if the message wasn't processed properly, it would bounce back to the sender. This is useful when you want to make sure that the message was processed properly and if not - revert the changes.

## Caveats[](#caveats)

Currently in TON bounced messages have only 224 usable data bits in the message and no references. This means that you can't recover much of the data from the bounced message. This is a limitation of the TON blockchain and will be fixed in the future. Tact helps you to check if your message fits the limit and if not - it will suggest to use a special type modifier `bounced<T>` for the receiver that would construct a partial representation that fits into the required limits.

## Bounced messages in Tact[](#bounced-messages-in-tact)

⚠️

Bounced text messages are not supported yet.

To receive a bounced message you need to define a `bounced` receiver in your contract or a trait:

```
contract MyContract {
    bounced(src: bounced<MyMessage>) {
        // ...
    }
}
```

To process bounced messages manually, you can use a fallback definition that handles raw `Slice` instead of a message:

```
contract MyContract {
    bounced(src: Slice) {
        // ...
    }
}
```
<!-- site: docs.tact-lang.org/book/external -->
# External Messages – TactGitHub
Tact Language Documentation
Book

External messages

# External Messages

⚠️

External message support must be enabled explicitly in the project configuration. Without enabling it compilation would fail.

External messages are those that don't have a sender and can be sent by anyone in the world. External messages are good tools for integrating with off-chain systems or for the general maintenance of contracts. Handling external messages is different from handling internal messages. In this section, we will cover how to handle external messages.

## How External Messages are Different[](#how-external-messages-are-different)

External messages are different from internal messages in the following ways:

### Contracts Pay for Gas Usage Themselves[](#contracts-pay-for-gas-usage-themselves)

When processing internal messages, the sender usually pays for gas usage. When processing external messages, the contract pays for gas usage. This means that you need to be careful with gas usage in external messages. You should always test the gas usage of your contracts and verify that everything is working as intended.

### Messages Have to Be Accepted Manually[](#messages-have-to-be-accepted-manually)

External messages are not accepted automatically. You need to accept them manually. This is done by calling the `acceptMessage` function. If you don't call the `acceptMessage` function, the message will be rejected. This is done to prevent the spamming of external messages.

### 10k Gas Limit Before Message Acceptance[](#10k-gas-limit-before-message-acceptance)

10k gas is a very small limit, and Tact itself can consume a sizable amount of gas before it even reaches your code. You should always test the gas usage of your contracts and verify that everything is working as intended.

📚️

The 10k gas limit for external messages is based on the parameter we set by the validator for the whole blockchain of the `gas_limit` field. You can take the reference here:

* [https://docs.ton.org/develop/smart-contracts/guidelines/accept#external-messages (opens in a new tab)](https://docs.ton.org/develop/smart-contracts/guidelines/accept#external-messages)
* [https://docs.ton.org/develop/howto/blockchain-configs#param-20-and-21 (opens in a new tab)](https://docs.ton.org/develop/howto/blockchain-configs#param-20-and-21)

### Unbounded Gas Usage After Message Acceptance[](#unbounded-gas-usage-after-message-acceptance)

After you accept the gas, the contract can use as much gas as it wants. This is done to allow the contract to carry out any kind of processing. You should always test the gas usage of your contracts and verify that everything is working as intended, and avoid possible vulnerabilities that could drain the contract balance.

### No Context Available[](#no-context-available)

When processing an external message, the `context` and `sender` functions are not available. This is because there is no context available for external messages. This means that you can't use the `context` and `sender` functions in external messages. You need to carefully test your contract to make sure that it doesn't use the `context` and `sender` functions.

## Enable External Messages Support[](#enable-external-messages-support)

To enable external messages support, please enable it in the project configuration file:

```
{
  "options": {
    "external": true
  }
}
```

## External receivers[](#external-receivers)

External receivers are defined the same way as internal ones, but using the `external` keyword instead of `receive`:

```
contract SampleContract {
    external("Check Timeout") {
 
        // Check for contract timeout
        require(self.timeout > now(), "Not timeouted");
 
        // Accept message
        acceptMessage();
 
        // Timeout processing
        self.onTimeouted();
    }
}
```
<!-- site: docs.tact-lang.org/book/lifecycle -->
# Message Lifecycle – TactGitHub
Tact Language Documentation
Book

Message lifecycle

# Message Lifecycle

There are several stages of message processing by a contract, there are more of them, but we would focus on the most important ones:

## Receive Phase[](#receive-phase)

This phase combines multiple low-level phases.

It starts by adding a **message value to the contract balance**. The value of an incoming message is the maximum price that a contract can pay for gas to process this message. The contract can overwrite this limit, but it is not recommended and is suitable only for advanced developers since it could lead to a contract being drained. 1 million of gas is the maximum amount that a contract can spend in a single contract which equals 1 TON (currently). If the message value is zero then execution is aborted.

Then some (usually a small amount) of nanotons is subtracted from the contract balance for storage. This means that you can't perfectly predict balance changes and have to adjust your code to this instability.

Then it deploys a contract if it is not deployed yet and the message contains the init package. If the init package isn't present, it will be ignored.

## Compute Phase[](#compute-phase)

This phase executes the code of a smart contract and produces a list of actions or an exception. Currently, only two types of actions are supported: **send message** and **reserve**.

Sending a message could use a fixed value or a dynamic value like **remaining value of a message** \- the remaining of value of the incoming message. Sending a message could be with a flag `SendIgnoreErrors` that would ignore errors during message sending and would continue to the next action. This flag is useful if you have multiple actions. When sending a message with some value, it first subtracts this value from the incoming value and only then from the contract balance (before processing).

## Action Phase[](#action-phase)

Actions are executed in sequence, but bear in mind:**EXCEPTION DURING PROCESSING ACTIONS WOULDN'T REVERT THE TRANSACTION**

For example, if you subtract 1 ton from a customer's balance and then send an invalid message, that could lead to a situation when the customer's balance is subtracted, but he wouldn't receive it.
<!-- site: docs.tact-lang.org/book/send -->
# Sending messages – TactGitHub
Tact Language Documentation
Book

Sending messages

# Sending messages

TON blockchain is message-based — to communicate with other contracts and to deploy new ones you need to send messages.

Messages in Tact are composed using a built-in [Struct](/book/structs-and-messages#structs) `SendParameters`, which consists of:

| Field  | Type                                   | Description                                                                                                                                 |
| ------ | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| bounce | [Bool](/book/types#primitive-types)    | When set to true (default) message bounces back to the sender if the receiver contract doesn't exist or wasn't able to process the message. |
| to     | [Address](/book/types#primitive-types) | Receiver internal [Address](/book/types#primitive-types) in TON blockchain.                                                                 |
| value  | [Int](/book/integers)                  | The amount of Toncoins you want to send with the message. This value is used to cover gas fees on the receiver side.                        |
| mode   | [Int](/book/integers)                  | An 8-bit value that configures how to send a message, defaults to 000. See: [Message mode](/book/message-mode).                             |
| body   | [Cell?](/book/types#primitive-types)   | [Optional](/book/optionals) message body as a [Cell](/book/types#primitive-types)                                                           |
| code   | [Cell?](/book/types#primitive-types)   | [Optional](/book/optionals) initial code of the contract (the compiled bytecode)                                                            |
| data   | [Cell?](/book/types#primitive-types)   | [Optional](/book/optionals) initial data of the contract (arguments of [init() function](/book/contracts#init-function) of the contract)    |

Fields `code` and `data` are what's called an [init package](/book/expressions#initof), which is used in deployments of new contracts.

## Send simple reply[](#send-simple-reply)

The simplest message is a reply to the incoming message returning all excess value of a message:

```
receive() {
    self.reply("Hello, World!".asComment()); // asComment converts a String to a Cell with a comment
}
```

## Send message[](#send-message)

If you need more advanced logic you can use the `send()` function and `SendParameters` [Struct](/book/structs-and-messages#structs) directly.

In fact, the previous example with [.reply()](/book/send#send-simple-reply) can be made using the following call to `send()` function:

```
receive() {
    send(SendParameters{
        // bounce is set to true by default
        to: sender(), // sending message back to the sender
        value: 0, // don't add Toncoins to the message...
        mode: SendRemainingValue | SendIgnoreErrors, // ...except for ones received from the sender due to SendRemainingValue
        body: "Hello, World".asComment(); // asComment converts a String to a Cell with a comment
    });
}
```

Another example sends a message to the specified [Address](/book/types#primitive-types) with a `value` of 111 TON and the `body` of a comment with a [String](/book/types#primitive-types) `"Hello, World!"`:

```
let recipient: Address = ...;
let value: Int = ton("1");
send(SendParameters{
    // bounce is set to true by default
    to: recipient,
    value: value,
    mode: SendIgnoreErrors, // will send the message despite any errors
    body: "Hello, World!".asComment()
});
```

The [optional flag](/book/message-mode#optional-flags) `SendIgnoreErrors` means that even when an error occurs during message sending next messages would be sent anyway. **No error during the sending phase would revert a transaction.**

## Send typed message[](#send-typed-message)

To send a binary typed message you can use the following code:

```
let recipient: Address = ...;
let value: Int = ton("1");
send(SendParameters{
    // bounce is set to true by default
    to: recipient,
    value: value,
    mode: SendIgnoreErrors, // don't stop in case of errors
    body: SomeMessage{arg1: 123, arg2: 1234}.toCell()
});
```

## Deploy contract[](#deploy-contract)

To deploy a contract you need to calculate its address and initial state with [initOf](/book/expressions#initof), then send them in the initialization message:

```
let init: StateInit = initOf SecondContract(arg1, arg2);
let address: Address = contractAddress(init);
let value: Int = ton("1");
send(SendParameters{
    // bounce is set to true by default
    to: address,
    value: value,
    mode: SendIgnoreErrors, // don't stop in case of errors
    code: init.code,
    data: init.data,
    body: "Hello, World!".asComment()  // not necessary, can be omitted
});
```
<!-- site: docs.tact-lang.org/book/message-mode -->
# Message mode – TactGitHub
Tact Language Documentation
Book

Message mode

# Message `mode`

As it was previously mentioned, messages are sent with the `mode` param of a struct `SendParameters`. It's an [Int](/book/integers) value, which is combined from base modes and optional flags, which are also [Int](/book/integers) values.

It's possible to use raw [Int](/book/integers) values and manually provide them for the `mode`, but for your convenience there's a set of constants which you may use to construct the compound `mode` with ease. Take a look at the following tables for more information on base modes and optional flags.

## Base modes[](#base-modes)

| Mode value | Constant name        | Description                                                                                                             |
| ---------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| 000        | \-                   | Ordinary message (default).                                                                                             |
| 646464     | SendRemainingValue   | Carry all the remaining value of the inbound message in addition to the value initially indicated in the new message.   |
| 128128128  | SendRemainingBalance | Carry all the remaining balance of the current smart contract instead of the value originally indicated in the message. |

## Optional flags[](#optional-flags)

| Flag value | Constant name          | Description                                                                                                                |
| ---------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| +1+1+1     | SendPayGasSeparately   | Pay forward fees separately from the message value.                                                                        |
| +2+2+2     | SendIgnoreErrors       | Ignore any errors arising while processing this message during the action phase.                                           |
| +16+16+16  | SendBounceIfActionFail | Bounce transaction in case of any errors during action phase. Has no effect if flag +2+2+2, SendIgnoreErrors is used.      |
| +32+32+32  | SendDestroyIfZero      | Current account must be destroyed if its resulting balance is zero (often used with mode 128128128, SendRemainingBalance). |

## Combining modes with flags[](#combining-modes-with-flags)

To make the [Int](/book/integers) value for `mode` field of SendParameters, you just have to combine base modes with optional flags by applying the [bitwise OR](/book/operators#binary-bitwise-or) operation.

For example, if you want to send a regular message and pay transfer fees separately, use the mode 000 (default) and a flag +1+1+1 to get `mode` \=1\= 1\=1, which is equal to using `SendPayGasSeparately` constant.

Alternatively, if you want to send the whole contract balance and destroy it immediately, use the mode 128128128 and flag +32+32+32 to get `mode` \=160\= 160\=160, which is equal to `SendRemainingBalance | SendDestroyIfZero`.

Here's how the latter example would look in code:

```
let to: Address = ...;
let value: Int = ton("1");
send(SendParameters{
    to: to,
    value: value,
    mode: SendRemainingBalance | SendDestroyIfZero,
    body: "Hello, World!".asComment()
});
```

⚠️

Note, that while adding ([+](/book/operators#binary-add)) base modes together with optional flags is possible, it is discouraged due to the possibility of excess values. Use the bitwise OR ([|](/book/operators#binary-bitwise-or)) instead, as it's designed to work with such flag and bit manipulations of the `mode`.

💡

Also note, that there can be only one [base mode](/book/message-mode#base-modes), but number of [optional flags](/book/message-mode#optional-flags) may vary: you can use them all, none or just some.
<!-- site: docs.tact-lang.org/book/deploy -->
# Deploy – TactGitHub
Tact Language Documentation
Book

Deployment

# Deploy

Tact Deployer is a small library that integrates with [TON Verifier (opens in a new tab)](https://verifier.ton.org) that allows you to deploy your contracts safely using your favorite wallet without needing to manage keys or deploy contracts manually. Tact Deployer also automatically verifies your contract's source code and you can be sure that your compiler is not compromised.

## Requirements[](#requirements)

Your contract MUST have the `Deployer` trait from the `@stdlib/deploy` package to be able to use Tact Deployer.

## Installation[](#installation)

To add Tact Deployer to your project, just use `yarn`:

```
yarn add @tact-lang/deployer
```

## How to use[](#how-to-use)

When you build your smart contracts using Tact, it produces a package (\*.pkg) file that has all the required information about the built smart contract. To deploy your smart contract, you need to create a deployer instance, pass your package file to it and provide initial data for your contract.

```
import * as fs from 'fs';
import * as path from 'path';
import { Address, contractAddress } from "ton";
import { SampleTactContract } from "./output/sample_SampleTactContract";
import { prepareTactDeployment } from "@tact-lang/deployer";
 
// Parameters
let testnet = true;                                 // Flag for testnet or mainnet
let packageName = 'sample_SampleTactContract.pkg';  // Name of your package to deploy
let outputPath = path.resolve(__dirname, 'output'); // Path to output directory
let owner = Address.parse('<put_address_here>');    // Our sample contract has an owner
let init = await SampleTactContract.init(owner);    // Create initial data for our contract
 
// Calculations
let address = contractAddress(0, init);     // Calculate contract address. MUST match with the address in the verifier
let data = init.data.toBoc();               // Create init data
let pkg = fs.readFileSync(                  // Read package file
    path.resolve(outputPath, packageName)
);
 
// Prepare deploy
let link = await prepareTactDeployment({ pkg, data, testnet });
 
// Present a deployment link and contract address
console.log('Address: ' + address.toString({ testOnly: testnet }));
console.log('Deploy link: ' + link);
```

After following this link you will be able to deploy and verify your smart contract.
<!-- site: docs.tact-lang.org/book/debug -->
# Debugging Tact Contracts – TactGitHub
Tact Language Documentation
Book

Debugging

# Debugging Tact Contracts

Tact has first-class support for Jest and `@tact-lang/emulator` for contract tests and debugging. The preferred type of test is the one with inline snapshots. They are easy to write and easy to debug.

For a quick start, it is better to use `tact-template` that has everything built in.

## Printing out debug data[](#printing-out-debug-data)

Tact has a built-in `dump` function that is similar to the one in FunC that allows you to print out data in your contract. It is very useful for debugging.

To make `dump` work you need to enable the feature `debug` in `tact.conf.json`.

Example:

```
dump("Hello World");
dump(123 + 444);
dump(a != null);
```

## Using `@tact-lang/emulator`[](#using-tact-langemulator)

Ton Emulator allows you to have a small virtual blockchain in your Node.js code. This library is built specifically for testing smart contracts in unit tests.

```
import { ContractSystem } from '@tact-lang/emulator';
import { sample_Contract } from './output/sample_Contract';
 
//
// Init System
//
 
// Contract System is a virtual environment that emulates the TON blockchain
const system = await ContractSystem.create();
 
// Treasure is a contract that has 1m of TONs and is a handy entry point for your smart contracts
let treasure = await system.treasure('name of treasure');
 
//
// Open contract
//
 
// Contract itself
let contract = system.open(sample_Contract.fromInit(treasure.address));
 
// This object would track all transactions in this contract
let tracker = system.track(contract.address);
 
// This object would track all logs
let logger = system.log(contract.address);
 
//
// Sending a message
//
 
// First we enqueue messages. NOTE: You can enqueue multiple messages in a row
await contract.send(treasure, { value: toNano(1) }, { $$type: "Deploy", queryId: 0n });
await contract.send(treasure, { value: toNano(1) }, { $$type: "Increment" });
 
// Run the system until there are no more messages
await system.run();
 
//
// Collecting results
//
 
console.log(track.collect()); // Prints out all transactions in contract
console.log(logger.collect()); // Prints out all logs for each transaction
 
//
// Invoking get methods
//
 
let counter = await contract.getCounter();
console.log(counter); // Prints out counter value
 
```

## Snapshot testing with `jest`[](#snapshot-testing-with-jest)

One of the most powerful features of Jest is the ability to write snapshot tests. Snapshot tests are a great way to test your contracts.

### Initial setup[](#initial-setup)

Example of a minimal test file:

```
import { ContractSystem } from '@tact-lang/emulator';
import { sample_Contract } from './output/sample_Contract';
 
describe("contract", () => {
    it("should deploy correctly", async () => {
 
        // Init test
        const system = await ContractSystem.create();
        const treasure = await system.treasure('my treasure');
        const contract = system.open(sample_Contract.fromInit(treasure.address));
        const tracker = system.track(contract.address);
 
        // Send a message
        await contract.send(treasure, { value: toNano(1) }, { $$type: "Deploy", queryId: 0n });
        await system.run();
 
        // Testing output
        expect(tracker.collect()).toMatchInlineSnapshot();
    });
});
```

### Generating snapshots[](#generating-snapshots)

After running the `yarn jest` command, the line with `toMatchInlineSnapshot` of the test will be automatically updated with a snapshot of the output.

```
// ...
        expect(tracker.collect()).toMatchInlineSnapshot(`
            [
              {
                "$seq": 0,
                "events": [
                  {
                    "$type": "deploy",
                  },
                  {
                    "$type": "received",
                    "message": {
                      "body": {
                        "cell": "x{946A98B60000000000000000}",
                        "type": "cell",
                      },
                      "bounce": true,
                      "from": "kQAI-3FJVc_ywSuY4vq0bYrzR7S4Och4y7bTU_i5yLOB3A6P",
                      "to": "kQBrSAP2y7QIUw4_1q0qciHfqdFmOYR9CC1oinn7kyWWRuoV",
                      "type": "internal",
                      "value": 1000000000n,
                    },
                  },
                  {
                    "$type": "processed",
                    "gasUsed": 6077n,
                  },
                  {
                    "$type": "sent",
                    "messages": [
                      {
                        "body": {
                          "cell": "x{AFF90F570000000000000000}",
                          "type": "cell",
                        },
                        "bounce": true,
                        "from": "kQBrSAP2y7QIUw4_1q0qciHfqdFmOYR9CC1oinn7kyWWRuoV",
                        "to": "kQAI-3FJVc_ywSuY4vq0bYrzR7S4Och4y7bTU_i5yLOB3A6P",
                        "type": "internal",
                        "value": 992727000n,
                      },
                    ],
                  },
                ],
              },
            ]
        `);
// ...
```

### Updating snapshots[](#updating-snapshots)

When you change your contract, your snapshots will be outdated. For example, gas usage or addresses were changed. To update them, you need to run the `yarn jest -u` command.
<!-- site: docs.tact-lang.org/book/upgrades -->
# Contracts upgrades – TactGitHub
Tact Language Documentation
Book

Contract upgrades

# Contracts upgrades

Tact currently doesn't allow contract upgrades since Tact contracts have a more convoluted nature than the ones in `func`. It is theoretically possible, but the required tools are not here.
<!-- site: docs.tact-lang.org/book/import -->
# Importing code – TactGitHub
Tact Language Documentation
Book

Importing code

# Importing code

Tact allows you to import Tact and [FunC (opens in a new tab)](https://docs.ton.org/develop/func/overview) code — any given `.tact` or `.fc`/`.func` file can be imported into your project using an `import` keyword.

Additionally, Tact compiler has a versatile set of standard libraries, which come bundled in, but not included right away, see [Language→Libs→Overview](/language/libs/overview).

⚠️

NOTE: All imported code is combined together with yours, so it's important to avoid name collisions and always double-check the sources!

## Import Tact code[](#import-tact-code)

It's possible to import any Tact code using the `import` statement and providing a relative path to the target `.tact` file like so:

```
import "./relative/path/to/the/target/tact/file.tact";
```

Specifying parent directories (`../`) is also possible:

```
import "../subfolder/imported/file.tact";
```

## Import FunC code[](#import-func-code)

It's possible to import code written in FunC code directly just as it's done with Tact code imports:

```
// Relative import
import "./relative/path/to/the/target/func/file.fc";
 
// Specifying parent directories
import "../subfolder/imported/func/file.fc";
```

But in order to use functions from such file, one has to declare them as `native` functions first. For example, when standard library [@stdlib/dns](/language/libs/dns) uses a `dns.fc` FunC file, it maps FunC functions to Tact ones like so:

```
// FunC code located in a file right next to the current Tact one:
import "./dns.fc";
 
// Mapping function signatures from FunC to Tact:
@name(dns_string_to_internal)
native dnsStringToInternal(str: String): Slice?;
```

## Standard libraries[](#standard-libraries)

See [Language→Libs→Overview](/language/libs/overview).
<!-- site: docs.tact-lang.org/book/config -->
# Configuration – TactGitHub
Tact Language Documentation
Book

Configuration

# Configuration

`tact.config.json` is an entry point to Tact projects. It is a JSON file that contains the list of all projects and compiler parameters.

## Project description[](#project-description)

Each project is described by the following fields:

* `name` is the name of a project. All generated files are prefixed with this name.
* `path` is the path to a root Tact file.
* `output` is the path to a directory where all generated files will be placed.
* `parameters` is a dictionary of parameters that will be passed to a compiler.
* `experimental` is a dictionary of experimental features that will be enabled for this project.

## Parameters[](#parameters)

Tact support next parameters:

* `debug: true`. Enables debug output of a contract. It is useful for debugging purposes. Enabling this contract would report that it was compiled in debug mode using the `supported_interfaces` method.
* `masterchain: true`. Enables [masterchain support](/language/guides/masterchain) in the contract.

## Example[](#example)

```
{
  "projects": [
    {
      "name": "sample",
      "path": "./sources/sample.tact",
      "output": "./sources/output",
      "options": {
        "debug": true,
        "experimental": {
          "inline": true
        }
      }
    }
  ]
}
```

## Experimental[](#experimental)

Tact support next experimental parameters:

* `inline: true`. Enables inlining of all functions in contracts. Could reduce gas usage at the cost of a bigger contract.
<!-- site: docs.tact-lang.org/book/masterchain -->
# Masterchain – TactGitHub
Tact Language Documentation
Book

Using masterchain

# Masterchain

⚠️

Masterchain support must be enabled explicitly in the project configuration. Without enabling it all masterchain addresses will be treated as invalid.

Most of the contract developers don't need a masterchain. Masterchain is needed only for participating in elections or storing libraries. If you don't need to participate in elections or store libraries, you don't need a masterchain.

## How contract is protected from masterchain[](#how-contract-is-protected-from-masterchain)

Most of the contracts written in `func` enforce all incoming addresses to be `basechain` ones. This is done to prevent masterchain addresses from being used in the contract. Tact does this **by default**.

What is prohibited to do without masterchain support enabled:

* Deploy contract to masterchain. The `init` function would throw a `Masterchain support is not enabled for this contract` error for every message.
* Receive messages from masterchain accounts.
* Receive structs that have masterchain addresses in them.
* Send messages to masterchain accounts.
* Use masterchain addresses in arguments of get methods.

## Enable masterchain support[](#enable-masterchain-support)

To enable masterchain support, please enable it in the project configuration file:

```
{
    "options": {
        "masterchain": true
    }
}
```
<!-- site: docs.tact-lang.org/book/func -->
# Compatibility with FunC – TactGitHub
Tact Language Documentation
Book

Compatibility with FunC

# Compatibility with FunC

Tact itself compiles to FunC and maps all its entities directly to various FunC and TL-B types.

## Convert types[](#convert-types)

[Primitive types](/book/types#primitive-types) in Tact are directly mapped to FunC ones.

All rules about copying variables are the same. One of the big differences is that there are no visible mutation operators in Tact and most [Slice](/book/types#primitive-types) operations mutate variables in place.

## Convert serialization[](#convert-serialization)

Serialization of [Structs](/book/structs-and-messages#structs) and [Messages](/book/structs-and-messages#messages) in Tact is automatic, unlike FunC where you need to define serialization logic manually.

Tact's auto-layout algorithm is greedy. This means that it takes the next variable, calculates its size, and tries to fit it into a current cell. If it doesn't fit, it creates a new cell and continues. All inner structs for auto-layout are flattened before allocation.

All optional types are serialized as `Maybe` in TL-B, except for [Address](/book/types#primitive-types).

There is no support for `Either` since it does not define what to pick during serialization in some cases.

### Examples[](#examples)

```
// _ value1:int257 = SomeValue;
struct SomeValue {
    value1: Int; // Default is 257 bits
}
```

```
// _ value1:int256 value2:uint32 = SomeValue;
struct SomeValue {
    value1: Int as int256;
    value2: Int as uint32;
}
```

```
// _ value1:bool value2:Maybe bool = SomeValue;
struct SomeValue {
    value1: Bool;
    value2: Bool?;
}
```

```
// _ cell:^cell = SomeValue;
struct SomeValue {
    cell: Cell; // Always stored as a reference
}
```

```
// _ cell:^slice = SomeValue;
struct SomeValue {
    cell: Slice; // Always stored as a reference
}
```

```
// _ value1:int256 value2:int256 value3:int256 ^[value4:int256] = SomeValue;
struct SomeValue {
    value1: Int as int256;
    value2: Int as int256;
    value3: Int as int256;
    value4: Int as int256;
}
```

```
// _ value1:int256 value2:int256 value3:int256 ^[value4:int256] flag:bool = SomeValue;
struct SomeValue {
    value1: Int as int256;
    value2: Int as int256;
    value3: Int as int256;
    flag: Bool; // Flag is written before value4 to avoid auto-layout to allocate it to the next cell
    value4: Int as int256;
}
```

```
// _ value1:int256 value2:int256 value3:int256 ^[value4:int256 flag:bool] = SomeValue;
struct SomeValue {
    value1: Int as int256;
    value2: Int as int256;
    value3: Int as int256;
    value4: Int as int256;
    flag: Bool;
}
```

```
// _ value1:int256 value2:^TailString value3:int256 = SomeValue;
struct SomeValue {
    value1: Int as int256;
    value2: String;
    value3: Int as int256;
}
```

## Convert received messages to `op` operations[](#convert-received-messages-to-op-operations)

Tact generates a unique `op` for every received typed message, but it can be overwritten.

The following code in FunC:

```
() recv_internal(int msg_value, cell in_msg_cell, slice in_msg) impure {
    ;; incoming message code...
 
    ;; Receive MessageWithGeneratedOp message
    if (op == 1180414602) {
        ;; code...
    }
 
    ;; Receive MessageWithOverwrittenOP message
    if (op == 291) {
        ;; code...
    }
 
}
```

Becomes this in Tact:

```
message MessageWithGeneratedOp {
    amount: Int as uint32;
}
 
message(0x123) MessageWithOverwrittenOP {
    amount: Int as uint32;
}
 
contract Contract {
    // Contract Body...
 
    receive(msg: MessageWithGeneratedOp) {
        // code...
    }
 
    receive(msg: MessageWithOverwrittenOP) {
        // code...
    }
 
}
```

## Convert `get`\-methods[](#convert-get-methods)

You can express everything except `list-style-lists` in Tact that would be compatible with FunC's `get`\-methods.

### Primitive return type[](#primitive-return-type)

If a `get`\-method returns a primitive in FunC, you can implement it the same way in Tact.

The following code in FunC:

```
int seqno() method_id {
    return 0;
}
```

Becomes this in Tact:

```
get fun seqno(): Int {
    return 0;
}
```

### Tensor return types[](#tensor-return-types)

In FunC there is a difference between tensor type `(int, int)` and `(int, (int))`, but for TVM there are no differences, they all represent a stack of two integers.

To convert the tensor that returned from a FunC `get`\-method, you need to define a [Struct](/book/structs-and-messages#structs) that has the same field types as the tensor and in the same order.

The following code in FunC:

```
(int, slice, slice, cell) get_wallet_data() method_id {
    return ...;
}
```

Becomes this in Tact:

```
struct JettonWalletData {
    balance: Int;
    owner: Address;
    master: Address;
    walletCode: Cell;
}
 
contract JettonWallet {
    get fun get_wallet_data(): JettonWalletData {
        return ...;
    }
}
```

### Tuple return type[](#tuple-return-type)

In FunC if you are returning a tuple, instead of a tensor you need to follow the process for a tensor type, but define the return type of a `get`\-method as optional.

The following code in FunC:

```
[int, int] get_contract_state() method_id {
    return ...;
}
```

Becomes this in Tact:

```
struct ContractState {
    valueA: Int;
    valueB: Int;
}
 
contract StatefulContract {
    get fun get_contract_state(): ContractState? {
        return ...;
    }
}
```

### Mixed tuple and tensor return types[](#mixed-tuple-and-tensor-return-types)

When some of the tensors are a tuple, you need to define a struct as in previous steps and the tuple one must be defined as a separate [Struct](/book/structs-and-messages#structs).

The following code in FunC:

```
(int, [int, int]) get_contract_state() method_id {
    return ...;
}
```

Becomes this in Tact:

```
struct ContractStateInner {
    valueA: Int;
    valueB: Int;
}
 
struct ContractState {
    valueA: Int;
    valueB: ContractStateInner;
}
 
contract StatefulContract {
    get fun get_contract_state(): ContractState {
        return ...;
    }
}
```

### Arguments mapping[](#arguments-mapping)

Conversion of `get`\-methods arguments is straightforward. Each argument is mapped _as-is_ to FunC one, and each tuple is mapped to a [Struct](/book/structs-and-messages#structs).

The following code in FunC:

```
(int, [int, int]) get_contract_state(int arg1, [int,int] arg2) method_id {
    return ...;
}
```

Becomes this in Tact:

```
struct ContractStateArg2 {
    valueA: Int;
    valueB: Int;
}
 
struct ContractStateInner {
    valueA: Int;
    valueB: Int;
}
 
struct ContractState {
    valueA: Int;
    valueB: ContractStateInner;
}
 
contract StatefulContract {
    get fun get_contract_state(arg1: Int, arg2: ContractStateArg2): ContractState {
        return ContractState{
            valueA: arg1,
            valueB: ContractStateInner{
                valueA: arg2.valueA,
                valueB: arg2.valueB
            }
        };
    }
}
```
<!-- site: docs.tact-lang.org/book/exit-codes -->
# Exit Codes – TactGitHub
Tact Language Documentation
Book

Exit codes

# Exit Codes

⚠️

THis page is under re-construction as per [#106 (opens in a new tab)](https://github.com/tact-lang/tact-docs/issues/106). All anchor links (`#`) may change in the future!

An exit code is a 161616\-bit unsigned integer which ranges between 000 to 655356553565535 (or 216−12\_{16} - 1216​−1).

Codes from 000 to 127127127 are allocated for FunC (TVM), 128128128 to 255255255 for Tact. The range from 256256256 to 655356553565535 is free for developer-defined exit codes.

List of pre-allocated exit codes:

| Exit Code  | phase                                                                                                                 | Description                                                                                                                 |
| ---------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 000        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Standard successful execution exit code                                                                                     |
| 222        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Stack underflow. Last op-code consumed more elements than there are on the stacks                                           |
| 333        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Stack overflow. More values have been stored on a stack than allowed by this version of TVM                                 |
| 444        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Integer overflow. Integer does not fit into −2256 ≤ x < 2256 or a division by zero has occurred                             |
| 555        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Integer out of expected range                                                                                               |
| 666        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Invalid opcode. Instruction is unknown in the current TVM version                                                           |
| 777        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Type check error. An argument to a primitive is of an incorrect value type                                                  |
| 888        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Cell overflow. Writing to builder is not possible since after operation there would be more than 1023 bits or 4 references  |
| 999        | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Cell underflow. Read from slice primitive tried to read more bits or references than there are                              |
| 101010     | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Dictionary error. Error during manipulation with dictionary (hashmaps)                                                      |
| 131313     | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | Out of gas error. Thrown by TVM when the remaining gas becomes negative                                                     |
| −14\-14−14 | [Compute phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#compute-phase)          | It means out of gas error, same as 131313. Negative, because it cannot be faked                                             |
| 323232     | [Action phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#transactions-and-phases) | Action list is invalid. Set during action phase if c5 register after execution contains unparsable object                   |
| 343434     | [Action phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#transactions-and-phases) | Action is invalid or not supported. Set during action phase if current action cannot be applied                             |
| 373737     | [Action phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#transactions-and-phases) | Not enough TON. Message sends too much TON (or there is not enough TON after deducting fees)                                |
| 383838     | [Action phase (opens in a new tab)](https://docs.ton.org/learn/tvm-instructions/tvm-overview#transactions-and-phases) | Not enough extra-currencies                                                                                                 |
| 128128128  | Tact (Compiler)                                                                                                       | Null reference exception — compiler expects an integer or cell but a null value has been passed                             |
| 129129129  | Tact (Compiler)                                                                                                       | Invalid serialization prefix — if there is any inconsistency with the previous op-code check, this exit code will be thrown |
| 130130130  | Tact (Compiler)                                                                                                       | Invalid incoming message — no suitable operation is found                                                                   |
| 131131131  | Tact (Compiler)                                                                                                       | Constraints error                                                                                                           |
| 132132132  | Tact (Compiler)                                                                                                       | Access denied — someone other than the owner sent a message to the contract                                                 |
| 133133133  | Tact (Compiler)                                                                                                       | Contract stopped — a message has been sent to a stopped contract                                                            |
| 134134134  | Tact (Compiler)                                                                                                       | Invalid argument — invalid Base64 string                                                                                    |
| 135135135  | Tact (Compiler)                                                                                                       | Code of a contract was not found — false flag for a dictionary call                                                         |
| 136136136  | Tact (Compiler)                                                                                                       | Invalid Address — Non 267267267\-bit Address or invalid chain id (other than 0 or -1)                                       |
| 137137137  | Tact (Compiler)                                                                                                       | Masterchain support is not enabled for this contract                                                                        |

Q: **Where to observe the list of all auto-generated exit codes in your project?**  
A: The Tact Compiler collects all exit codes at the end of a \*.md file and you can track them in the directory along the path "./ProjectFolder/build/ProjectName/tact\_ProjectName.md"

Q: **How to observe a thrown exit code?**  
A: In Tact, it's not wise to print the transactions to see the results because they are not easy to read. If you want to see the exit code of a transaction, use the below template in your Typescript local tests:

```
const sender = await blockchain.treasury('sender');
const result = await contractName.send(sender.getSender(), { value: toNano('0.05'), }, { transactionData });
 
expect(result.transactions).toHaveTransaction(
    { from: sender.address, to: contractName.address, exitCode: YOUR_DESIRED_EXIT_CODE }
);
```

* First line defines the sender.
* Second line sends the transaction.
* In the third line, you check if the result has a transaction from sender to your contract with your desired exit code.

## Compute phase[](#compute-phase)

### 000: Successful execution[](#0)

This exit code means that the Compute phase of the transaction was completed successfully.

### 444: Integer overflow[](#4)

In TVM, integer can be in the range -2256 < x < 2256. If the value during the calculation went beyond this range, then 4 exit code is thrown.

Example:

```
self.id = 1; // force not to ignore it by using storage variables
repeat(256) {
    self.id = 2 * self.id;
}
```

### 555: Integer out of expected range[](#5)

If the integer value went beyond the expected range, then 5 exit code is thrown. For example, if a negative value was used in the .store\_uint() function. In Tact, there are some other new situations such as:  
1- As you know, you can define more limited integers in Tact (integers with less than 257 bits). If you try to store a number in this kind of integers and the number doesn't fit to this limited range, you will face this exit code.  
2- according to `storeUint(self: Builder, value: Int, bits: Int)` function, it's not possible to use `storeUint(0, 257)` because `0 ≤ bits ≤ 256`.

Example:

```
// option 1 -> id: Int as uint32
self.id = 1; // force not to ignore it by using storage variables
repeat(32) {
    self.id = 2 * self.id;
}
 
// option 2 -> according to storeUint(self: Builder, value: Int, bits: Int) function, it's not possible to use storeUint(0, 1024) because 0 ≤ bits ≤ 256
let s: Slice = beginCell().storeUint(0, 257).asSlice();
```

### 888: Cell overflow[](#8)

A cell has the capacity to store 1023 bits of data and 4 references to other cells. If you try to write more than 1023 bits or more than 4 references, 8 exit code is thrown.

Example:

```
// according to storeUint(self: Builder, value: Int, bits: Int) function, it's not possible to use storeUint(0, 1024) because 0 ≤ bits ≤ 256
let s: Slice = beginCell().storeUint(0, 256).storeUint(0, 256).storeUint(0, 256).storeUint(0, 256).asSlice();
```

### 999: Cell underflow[](#9)

If you try to read more data from a slice than it contains, then 9 exit code is thrown.

Example:

```
let s: Slice = emptySlice();
self.id = s.loadUint(1); // force not to ignore it by using storage variables
```

### 131313: Out of gas error[](#13)

If there isn't enough TON to handle compute phase, this error is thrown.

During processing, the NOT operation is applied to this value, which changes this value to -14\. This is done so that this exit code cannot be faked using the throw function, since all such functions accept only positive values for the exit code as it was discussed previously.

Example:

```
repeat(10000) {
    self.id += 1;
}
```

## Action phase[](#action-phase)

### 343434: Action is invalid or not supported[](#34)

This exit code is responsible for most of the errors when working with actions: invalid message, incorrect action, and so on.

Example:

```
nativeSendMessage(emptyCell(), 0);
```

### 373737: Not enough TON[](#37)

It means that there isn't enough TON to send the specified amount of it.

Example:

```
send(SendParameters{to: context().sender, value: ton("10")});
```

## Tact (Compiler)[](#tact-compiler)

### 130130130: Invalid incoming message[](#130)

When you send a message to a contract, the first 32 bits of message body is the op code. It determines the operation that must be done. In FunC, if no op code is found, 0xffff will be thrown. In Tact, 130 exit code will be thrown.

Example:

1. First, define an empty contract like below:

```
contract Fireworks {
    init() {}
}
```

1. Then, send a message to this contract. Because no suitable operation is found, you will get this exit code.

### 132132132: Access denied[](#132)

First, you should import and inherit from Ownable Trait. After it, your contract will have an owner. You can ask for a check by calling `self.requireOwner();` in your functions. It will ensure that only the owner can send message to your contract.

Example:

```
import "@stdlib/deploy";
import "@stdlib/ownable";
 
message FakeLaunch {
 
}
 
contract Fireworks with Deployable, Ownable {
    owner: Address;
 
    init(){
        self.owner = sender();
    }
 
    receive(msg: FakeLaunch){
        self.requireOwner();
    }
}
 
fun requireOwner() {
        nativeThrowUnless(132, sender() == self.owner);
}
```

### 133133133: Contract stopped[](#133)

The stoppable trait allows to stop the contract. If you send a message to a stopped contract, and the contract asks for a check by running `self.requireNotStopped();`, this exit code will be thrown. In the current version of Tact, 40368 exit code will be thrown instead of 133.

Example:

```
import "@stdlib/deploy";
import "@stdlib/ownable";
import "@stdlib/stoppable";
 
message FakeLaunch {}
 
contract Fireworks with Deployable, Ownable, Stoppable {
    owner: Address;
    stopped: Bool;
 
    init() {
        self.owner = sender();
        self.stopped = false;
    }
 
    receive(msg: FakeLaunch) {
        self.stopped = true;
        self.requireNotStopped();
    }
}
 
fun requireNotStopped() {
    require(!self.stopped, "Contract stopped");
}
```

### 134134134: Invalid argument[](#134)

This will be thrown by the below FunC function(in the last part of a bunch of if conditions). This function reads something from Base64.

If the input characters don't fit into base64 chars, you will encounter this exit code.

Example:

```
let code: Slice = beginCell().storeUint(0, 8).asSlice().fromBase64();
// 0 is not a valid ASCII code so it cannot be converted to Base64
```

### 135135135: Code of a contract was not found[](#135)

It will check the return flag of a search on the dictionary keys.

Example:

```
// copy & paste the below line in wrapper file(../build/ContractName/tact_ContractName.ts) instead of the second line of ContractName_init() function - this is a dictionary containing another smart contract code which leads to 135 exit code
// const __system = Cell.fromBase64('te6cckECIwEAB1EAAQHAAQEFodSXAgEU/wD0pBP0vPLICwMCAWIPBAIBIA0FAgEgDAYCAUgLBwIBIAkIAHWs3caGrS4MzmdF5eotqc1vCmiu5ihm5iaqaEpGiYzo5syoyYptJmhuDSoKamwmziqo5spNKy0NLapwQAIRrt7tnm2eNijAIAoAAiQAEbCvu1E0NIAAYACVu70YJwXOw9XSyuex6E7DnWSoUbZoJwndY1LStkfLMi068t/fFiOYJwIFXAG4BnY5TOWDquRyWyw4JwnZdOWrNOy3M6DpZtlGbopIAhG+KO7Z5tnjYowgDgACIwN+0AHQ0wMBcbCjAfpAASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IhUUFMDbwT4YQL4Yts8VRTbPPLggts8IBIQARbI+EMBzH8BygBVQBEA8lBUINdJgQELuvLgiCDXCwoggQT/uvLQiYMJuvLgiM8WWCDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IjPFgEgbpUwcAHLAY4eINdJgQELuvLgiCDXCwoggQT/uvLQiYMJuvLgiM8W4hL0AAHIgQEBzwDJAczJ7VQC9gGSMH/gcCHXScIflTAg1wsf3iCCEIQwhou6jtYw0x8BghCEMIaLuvLggfpAASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IgBgQEB1wD6QAEg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIQzBsE+AgghAF6DTmuhkTAvyO0DDTHwGCEAXoNOa68uCB+kABINdJgQELuvLgiCDXCwoggQT/uvLQiYMJuvLgiAH6QAEg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIEmwS4CCCEHKDsbi6jpQw0x8BghByg7G4uvLggdQBMds8f+DAAAHXScEhsJF/4HAXFATw+EFvJBAjXwMkbrOOF4ERTVNxxwWSMX+ZJSBu8tCAWMcF4vL0mSaBEU0CxwXy9OL4ACDIAYIQcoOxuFjLH8zJI9s8kyBus48kICBu8tCAbyIxggkxLQAjfwNwQwNtbds8IG7y0IBvIjBSQNs86FtwgwYmA39VMG1tFh4dFQEE2zweADSBAQH0hG+lwP+dIG7y0IABIG7y0IBvAuBbbQLQNPhBbyQQI18D+ENUECfbPAGBEU0CcFnIcAHLAXMBywFwAcsAEszMyfkAyHIBywFwAcsAEsoHy//J0CDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IgixwXy9ANwgEBwVSBtbW3bPH8YHgDaAtD0BDBtAYIA6ksBgBD0D2+h8uCHAYIA6ksiAoAQ9BfIAcj0AMkBzHABygBAA1kg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIzxYBINdJgQELuvLgiCDXCwoggQT/uvLQiYMJuvLgiM8WyQKi+EFvJDAyJ26zjheBEU1ToccFkjF/mSggbvLQgFjHBeLy9JkpgRFNAscF8vTiJYEBASRZ9AxvoZIwbd9ujo8TXwNwgEBwVSBtbW3bPAHjDQF/HhoC+iTBFI72FYEBAVQQNCBulTBZ9FowlEEz9BTiA6QBggr68IChJnAGyFmCEAXoNOZQA8sfASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IjPFgEg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIzxbJQVBDMHABbW3bPOMOHhsD6jBTQds8IG6OhDAk2zzeIG7y0IBvIjFwUEOAQAPIVSCCEIQwhotQBMsfWCDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IjPFoEBAc8AASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IjPFsl/VTBtbds8AR0cHgA0gQEB9IxvpcD/nSBu8tCAASBu8tCAbwLgW20ANgGBAQH0eG+lwP+dIG7y0IABIG7y0IBvAuBbbQHKyHEBygFQBwHKAHABygJQBSDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IjPFlAD+gJwAcpoI26zkX+TJG6z4pczMwFwAcoA4w0hbrOcfwHKAAEgbvLQgAHMlTFwAcoA4skB+wAfAJh/AcoAyHABygBwAcoAJG6znX8BygAEIG7y0IBQBMyWNANwAcoA4iRus51/AcoABCBu8tCAUATMljQDcAHKAOJwAcoAAn8BygACyVjMArjtRNDUAfhj0gAB4wL4KNcLCoMJuvLgifpAASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IgB+kABINdJgQELuvLgiCDXCwoggQT/uvLQiYMJuvLgiBIC0QHbPCIhAAgBbW1wAPr6QAEg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIAfpAASDXSYEBC7ry4Igg1wsKIIEE/7ry0ImDCbry4IgB+kAh1wsBwwCOHQEg10mBAQu68uCIINcLCiCBBP+68tCJgwm68uCIkjFt4gH0BNQB0IEBAdcAMBUUQzBsFUhhij0=');
let ctx: Context = context();
let fireworks_init: StateInit = initOf Fireworks(0);
```

### 136136136: Invalid address[](#136)

In TON, all addresses are 267 bits. If you violate this rule, you will face this exit code.

Currently, TON only supports two chain id. 0 for basechain and -1 for masterchain. If you address isn't from basechain, 136 exit code will be thrown.

Example:

```
// fun newAddress(chain: Int, hash: Int): Address;
// creates a new address from chain and hash values.
let zeroAddress: Address = newAddress(1, 0); // invalid chain zero address
```

### 137137137: Masterchain support is not enabled for this contract[](#137)

Currently, TON only supports two chain id. 0 for basechain and -1 for masterchain.

Tact only supports basechain and if you address is from masterchain, 137 exit code will be thrown.

Example:

```
// fun newAddress(chain: Int, hash: Int): Address;
// creates a new address from chain and hash values.
let zeroAddress: Address = newAddress(-1, 0); // masterchain zero address
```
<!-- site: docs.tact-lang.org/book/programmatic -->
# Programmatic API – TactGitHub
Tact Language Documentation
Book

Programmatic API

# Programmatic API

You can invoke the Tact compiler from your code in node and browser environments.

⚠️

This API has not been released yet. It will be released in the 1.0.0 version.

## Run compiler in browser[](#run-compiler-in-browser)

```
import { run } from "@tact-lang/compiler";
 
// Virtual FS
const fs = {
  ["main.tact"]: Buffer.from("...").toString("base64"),
};
 
const config = {
  projects: [
    {
      name: "Sample",
      path: "main.tact",
      output: "./output",
    },
  ],
};
 
// Run compiler
let successful = await run({ config, fs });
 
// NOTE: Output from is written to the same fs object.
```

## Contract verification[](#contract-verification)

You can always verify the compiled package with the `verify` function.

```
import { verify } from "@tact-lang/compiler";
const pkg: string = '...';
const res = await verify(pkg);
```
