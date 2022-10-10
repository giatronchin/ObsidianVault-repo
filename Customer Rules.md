#### How to create Custom Rules

#john-the-ripper

[[John The Ripper]]'s Custom rules are defined in the `john.conf` file, usually located in `/etc/john/john.conf` if you have installed John using a package manager or built from source with `make`.


`[List.Rules:YourRule]` - Is used to define the name of your rule, this is what you will use to call your custom rule as a John argument.

We then use a regex style pattern match to define where in the word will be modified, again- we will only cover the basic and most common modifiers here:

`Az` - Takes the word and appends it with the characters you define  

`A0` - Takes the word and prepends it with the characters you define  

`c` - Capitalises the character positionally

The following is a classic regex syntax. An example may appear like this:

```
[List.Rules:PoloPassword]

cAz"[0-9] [!£$%@]"
```

A more detailed wiki about it [here](https://www.openwall.com/john/doc/RULES.shtml) in order to get a full view of the types of modifier you can use, as well as more examples of rule implementation.

The right command to use this custom rule within John the Ripper is:

```bash
	john --wordlist=[path to wordlist] --rule=PoloPassword [path to file]
```

