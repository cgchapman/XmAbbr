# **XmAbbr (XML Abbreviated)**

## Introduction

XmAbbr is a lightweight abbreviated alternative serialisation language for XML (version 1.1) formatted data and can represent any standalone XML file. In addition the language specifies a computer language independent XML representation of structured typed data together with a series of mapping conventions for use with specific computer languages and applications.

XmAbbr is intended to be easier to read and edit (without needing special tools) than XML plus also facilitating standardised methods for representing structured data for direct import and export from a broad range of applications and computer languages. Anticipated uses range from messaging between applications to configuration files and simplified export or import of XML files.

Conversion to and from between XML and XmAbbr formatted data results in no semantic loss of information. In addition XmAbbr serves as an object notation – for example a round trip conversion between JSON formatted data and XmAbbr (ie: from JSON to XmAbbr and back again) also results in no loss of semantic information.

The XmAbbr format has a strong visual resemblance to YAML and is intended to be presented in an indented style though (as with XML) it does not use indentation as a markup element. Where used to convey data type information a form of “Duck Typing” (inferred data type) is employed. For XmAbbr data programmatically generated this is designed to be unambiguous and the rules for avoiding confusion between text and typed data in the case of manual editing are simple and clear.

This document is not a specification but where used the capitalised words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and       "OPTIONAL" have the meanings as described in RFC 2119. Where definitions of grammar are given these use the XML Extended Backus-Naur Form (EBNF) notation.

XmAbbr has three distinct constituent parts:

1. An alternate markup language for representing XML data which fully covers all features of XML except for external DTDs (Document Type Definitions) and Schemas which alter the XML content (All XML files generated from XmAbbr content have the XML declaration attribute standalone = "yes"). One aim of the language (as far as possible) is for most people to be able to infer from an example what the basic structure and meaning of this is.

2. A definition of how high level data constructs (such as objects, arrays and data types) are represented and interpreted within XML structured data. So, while XmAbbr can be used for any XML data, in the case of structured data a subset of XML is used. This also includes some naming and layout conventions for XML elements and attributes.

3. Specific definitions for various computer languages and environments as to how structured XML / XmAbbr data is to be both interpreted and generated. Of these JavaScript is the main target and XmAbbr is able to fulfil the same functions as JSON (eg: direct import and export of JavaScript objects). Data in the JSON format can be converted to the XML / XmAbbr format and converted back again without loss of information.

## 1. Representing XML format data in XmAbbr

The two types of content in XmAbbr (as with XML) are mark up and data. Mark up is contained in markup tags or the use of quoted text and is identified by four markup character definitions. These are a space or non printinable character, “:”, ‘”’ (Double Quote) and “’” (Single Quote or Apostrophe). These have a meaning analogous to the use of “<”, “>”, ‘”’ and “’” in XML. 

A markup tag is a sequence of characters enclosed by a leading white space character and a trailing “:”.  Quoted text is a sequence of characters enclosed within matched ‘”’ or “’” characters with the enclosing quote character represented within this by being repeated. This format is very familiar to those who use applications such as CSV files and spreadsheet formulas etc.

The basic form of a markup tag is one or more optional control characters followed by a property name. The primary control symbols used are “-” to indicate down one level and “+” (one or more) to indicate up one or more levels. The following example is of data for an element called peopleList containing elements called name which in turn contains elements named Honorific, firstName, middlename, lastName, suffix and gender.

${\color{blue}peopleList:}$  <br>
         ${\color{blue}− name: \space }$  <br>
                  {\color{blue}−Honorific: \space \color{red}Mr \space \space \space \space \color{blue}firstName: \space \color{red}John \space \space \space \space \space \space \color{blue}middleName: \space \color{red}Albert\space \space \space  \space \color{blue}lastName: \space \color{red}Doe \space \space \space \space \space \color{blue}gender: \space \color{red}Male}$  <br>
         ${\color{blue}＋name:  \space }$  <br>
                  ${\color{blue}−Honorific: \space \color{red}Ms \space \space \space \space \color{blue}firstName: \space \color{red}Edith \space \space \space \space \space \color{blue}middleName: \space \color{red}Jane Lousie \space \space \space  \space \color{blue}lastName: \space \color{red}Pargetter \space \space \space \space \space \color{blue}gender: \space \color{red}Female}$  <br>
                  <br>
*(Markup tags are shown in blue and data in red)* <br>
<details>
<summary> Expand for Conversion to XML </summary>

${\color{blue}ᐸpeopleList}$  <br>
         ${\color{blue}ᐳᐸname}$  <br>
                  ${\color{blue}ᐳᐸHonorificᐳ \color{red}Mr \color{blue}ᐸ/HonorificᐳᐸfirstNameᐳ \color{red}John \color{blue} ᐸ/firstNameᐳᐸmiddleNameᐳ \color{red}Albert \color{blue} ᐸ/middleName}$  <br>
                  ${\color{blue}ᐳᐸlastNameᐳ \color{red}Doe \color{blue}ᐸ/lastNameᐳᐸgenderᐳ \color{red}Male \color{blue} ᐸ/gender}$  <br>
         ${\color{blue}ᐳᐸname }$  <br>
                  ${\color{blue}ᐳᐸHonorificᐳ \color{red}Ms \color{blue}ᐸ/HonorificᐳᐸfirstNameᐳ \color{red}Edith \color{blue} ᐸ/firstNameᐳᐸmiddleNameᐳ \color{red}Jane Lousie  \color{blue} ᐸ/middleName}$  <br>
                 ${\color{blue}ᐳᐸlastNameᐳ \color{red}Pargetter \color{blue}ᐸ/lastNameᐳᐸgenderᐳ \color{red}Female \color{blue} ᐸ/genderᐳ}$  <br>
</details>



### 1.1 Summary of markup notation 
<details>
<summary>   Details </summary>

Before processing any XmAbbr input data is normalised to Unicode NFC (Normalisation Form Canonical Composition). This is however applied before any numeric character references are decoded. Any space or other non-printable character is treated as a word or token delimiter unless within quoted text

#### 1.1.1 Property Name rules

Property names are either standard names or a form of quoted text. A standard name follows the form common in most computer languages of variable names starting with a visible character which is not a symbol nor an ASCII decimal digit (0–9); with digits allowed for following characters. In addition the underline (“_”) character is allowed as both a starting and following character while  hyphen minus (“-”) can be a following character. Within XML the additional characters of “:” and “.” are allowed in property names but are used for specific purposes (such as namespaces) and are not allowed in XmAbbr standard names.
 
<details>
<summary> XML Extended Backus-Naur Form (EBNF) definition </summary>

The rules of the grammar used in XmAbbr for representing XML data are as follows:
<details>
<summary> Expand for a definition of XML EBNF notation </summary>
Each rule in the grammar defines a symbol in the format:  symbol ::= expression<br>

With an expression consisting of various combinations of basic terms, operators and previously defined symbols. Basic terms define a string of one or more characters and consist of:<br>

 * Literal quoted strings – enclosed in by either a single or double quote character (eg: “XXX” or ‘XXX’).
* Unicode character numeric references (ISO 10646) - of the form #xH where H is a hexadecimal integer or #N where N is a base 10 integer (eg: #xF6, #x1F2B2, #67, #65421).
* A character class – enclosed between “[“ and “]” characters and defining one or many possible     values for a single character. This may include either one or more literal characters or one or more Unicode character numeric references and may specify ranges by using a “-” character between two characters or character references. In addition a character class my specify any character except for those given by starting the class with “[^”. Examples of character classes are:

  - #x6D or #155 or [m]  or “m” - all match the single character “m”
  - [#x61-#x7A] or [#141-#172] or [a-z] – all match the lowercase letters “a” to “z”
  - [A-Za-z] or [#141-#172#x41-#x5A] or [a-z] | [A-Z] – all match all letters in the ASCII range
  - [^a-z] or [^#x61-#x7A] – matches any character that is not in the range “a” to “z”

Operators which may be used in combination with basic terms or expressions in order of precedence are:<br>


* Brackets – enclosing expressions in opening and closing brackets “(“ and “)” alters the order of expression evaluation or can be used to make this clear. 
* Occurrence operators – these are postfix operators which follow an expression or term and define how many times this may occur. They are “?” for a single occurrence which is optional, “*” for zero or many occurrences and “+” for one or more. If no occurrence operator is used then the expression or term must occur once.
* But Not operator – this is the infix operator “-” and defines a result which matches the left hand side expression but does not match the right hand side expression. 
* Concatenation – this is an infix operator implied by space(s) between expressions meaning expressions applied in sequence.
* Or operator – this is the infix operator “|” meaning a result which matches either expression.

Examples of expressions are:<br>

* ( [ก-๛]  |  [0-9] )+ – matches one or more characters which are Thai characters or ASCII digits.
* ( “w” | “t” ) “alk” ( “ing” | “ed” | “s” ) ?  - matches any of the words walk, walking, walked, walks, talk, talking, talked or talks (The trailing “?” operator make the alternative endings optional).
* [#x21-#xFE] – [0-9] – matches any printable ASCII character except digits.




Refer to [Extensible Markup Language (XML) 1.1 – Notation ](https://www.w3.org/TR/xml11/#sec-notation)

</details>


 **SingleQuotedText** ::=  [‘] ( [^'] | ( [‘] [‘] ) ) * [‘]<br>
 **DoubleQuotedText** ::=  [“] ( [^”] | ( [“] [“] ) ) * [“]<br>
 **QuotedText** ::=  SingleQuotedText | DoubleQuotedText<br>
 **NonPrintingChar** ::=  [#x00-#x20] | #x7F-#xA0 | [#x2000-#x200A] |#x2028 | #x2029 | #x202F | #x205F | #x3000 <br>
 **WhiteSpace** ::= NonPrintingChar +<br>
 **PrintingChar** ::= [#x21-#x7E] | [#xA1-#x1FFF] | [#x200B-#x2027] | [#x202A-#x202E] | [#x2030-#x205E] | [#x2060-#x2FFF] | [#x3001-#x10FFFF]<br>
 **TokenChar** ::= PrintingChar - [:‘“] <br>
 **TokenText** ::= TokenChar +<br>
 **DecDigit** ::= [0-9]<br>
 **HexDigit** ::= [0-9a-fA-F]<br>
 **HexCharRef** ::= “#” ? [xX] HexDigit HexDigit ?  HexDigit ? HexDigit ? HexDigit ? HexDigit ?<br>
 **DecCharRef** ::= “#” ? DecDigit DecDigit ? DecDigit ? DecDigit ? DecDigit ? DecDigit ? DecDigit ?<br>
 **NumCharRef** ::=   HexCharRef | DecCharRef<br>
 **InterQuoteToken** ::=  TokenText - NumCharRef<br>
 **QuotedTokenOrRefs** ::= QuotedText ( ( NumCharRef | InterQuoteToken ) QuotedText ) + <br>
 **Text** ::= WhiteSpace? ( ( TokenText |  QuotedTokenOrRefs | QuotedText ) WhiteSpace ) +<br>




 **NameStartChar** ::=  | [A-Z] | "_" | [a-z] | [#xC0-#xD6] | [#xD8-#xF6] | [#xF8-#x2FF] | [#x370-#x37D] | [#x37F-#x1FFF] | [#x200C-#x200D] | [#x2070-#x218F] | [#x2C00-#x2FEF] | [#x3001-#xD7FF] | [#xF900-#xFDCF] | [#xFDF0-#xFFFD] | [#x10000-#xEFFFF]<br>
 **NameChar** ::= NameStartChar | "-" | [0-9] | #xB7 | [#x0300-#x036F] | [#x203F-#x2040]<br>
 **Name** ::= NameStartChar ( NameChar ) *<br>
Names ::= Name ( #x20 Name )*
Nmtoken ::= ( NameChar )+
Nmtokens ::= Nmtoken ( #x20 Nmtoken )*
</details>

</details>

### 1.1 Rules for property names and detailed markup tag format 
<details>
<summary>   Details </summary>

Insert text here

</details>

### 1.3 Extended markup tag control symbols definition and usage
 
<details>
<summary>   Details </summary>

Insert text here

</details>

### 1.4 Text formats and options 
<details>
<summary>   Details </summary>

Insert text here

</details>
### 1.5 Examples 
<details>
<summary>   Details </summary>

**ᐸ?xml** version="1.0" encoding="UTF-8"**?ᐳ** <br>
**ᐸDocument** xmlns="urn:iso:std:iso:20022:tech:xsd:pain.008.001.02" xmlns:xsi="www,w3,org/2001/XMLSchema-instance" **ᐳ** <br>
        **ᐸCstmrDrctDbtInitnᐳ** <br>
                **ᐸGrpHdrᐳ** <br>
                        **ᐸMsgIdᐳ**Message 9 200131106**ᐸ/MsgIdᐳ** <br>
                        **ᐸCreDtTmᐳ**2013-10-08T12:57:52**ᐸ/CreDtTmᐳ** <br>
                        **ᐸNbOfTxsᐳ**2**ᐸ/NbOfTxsᐳ** <br>
                        **ᐸCtrlSumᐳ**0.30**ᐸ/CtrlSumᐳ** <br>
                        **ᐸInitgPtyᐳ** <br>
                                **ᐸNmᐳ**PILOTFORETAG B**ᐸ/Nmᐳ** <br>
                                **ᐸIdᐳ** <br>
                                        **ᐸOrgIdᐳ** <br>
                                                **ᐸOthrᐳ** <br>
                                                        **ᐸIdᐳ**3321251633**ᐸ/Idᐳ** <br>
                                                        **ᐸSchmeNmᐳ** <br>
                                                                **ᐸCdᐳ**BANK**ᐸ/Cdᐳ** <br>
                                                        **ᐸ/SchmeNmᐳ** <br>
                                                **ᐸ/Othrᐳ** <br>
                                        **ᐸ/OrgIdᐳ** <br>
                                **ᐸ/Idᐳ** <br>
                        **ᐸ/InitgPtyᐳ** <br>
                **ᐸ/GrpHdrᐳ** <br>
                **ᐸPmtInfᐳ** <br>
                        **ᐸPmtInfIdᐳ**SEND PAYMENT VER 009**ᐸ/PmtInfIdᐳ** <br>
                        **ᐸPmtMtdᐳ**DD**ᐸ/PmtMtdᐳ** <br>
                        **ᐸBtchBookgᐳ**true**ᐸ/BtchBookgᐳ** <br>
                        **ᐸNbOfTxsᐳ**2**ᐸ/NbOfTxsᐳ** <br>
                        **ᐸCtrlSumᐳ**0.30**ᐸ/CtrlSumᐳ** <br>
                        **ᐸPmtTpInfᐳ** <br>
                                **ᐸSvcLvlᐳ** <br>
                                        **ᐸCdᐳ**SEPA**ᐸ/Cdᐳ** <br>
                                **ᐸ/SvcLvlᐳ** <br>
                                **ᐸLclInstrmᐳ** <br>
                                        **ᐸCdᐳ**B2B**ᐸ/Cdᐳ** <br>
                                **ᐸ/LclInstrmᐳ** <br>
                                **ᐸSeqTpᐳ**RCUR**ᐸ/SeqTpᐳ** <br>
                        **ᐸ/PmtTpInfᐳ** <br>
                        **ᐸReqdColltnDtᐳ**2013-11-08**ᐸ/ReqdColltnDtᐳ** <br>
                        **ᐸCdtrᐳ** <br>
                                **ᐸNmᐳ**PILOTFORETAG B**ᐸ/Nmᐳ** <br>
                                **ᐸPstlAdrᐳ** <br>
                                        **ᐸCtryᐳ**DE**ᐸ/Ctryᐳ** <br>
                                **ᐸ/PstlAdrᐳ** <br>
                        **ᐸ/Cdtrᐳ** <br>
                        **ᐸCdtrAcctᐳ** <br>
                                **ᐸIdᐳ** <br>
                                        **ᐸIBANᐳ**NL58HAND0718128982**ᐸ/IBANᐳ** <br>
                                **ᐸ/Idᐳ** <br>
                        **ᐸ/CdtrAcctᐳ** <br>
                        **ᐸCdtrAgtᐳ** <br>
                                **ᐸFinInstnIdᐳ** <br>
                                        **ᐸBICᐳ**HANDNL2A**ᐸ/BICᐳ** <br>
                                **ᐸ/FinInstnIdᐳ** <br>
                        **ᐸ/CdtrAgtᐳ** <br>
                        **ᐸCdtrSchmeIdᐳ** <br>
                                **ᐸIdᐳ** <br>
                                        **ᐸPrvtIdᐳ** <br>
                                                **ᐸOthrᐳ** <br>
                                                        **ᐸIdᐳ**SE92ZZZ3321251633**ᐸ/Idᐳ** <br>
                                                        **ᐸSchmeNmᐳ** <br>
                                                                **ᐸPrtryᐳ**SEPA**ᐸ/Prtryᐳ** <br>
                                                        **ᐸ/SchmeNmᐳ** <br>
                                                **ᐸ/Othrᐳ** <br>
                                        **ᐸ/PrvtIdᐳ** <br>
                                **ᐸ/Idᐳ** <br>
                        **ᐸ/CdtrSchmeIdᐳ** <br>
                        **ᐸDrctDbtTxInfᐳ** <br>
                                **ᐸPmtIdᐳ** <br>
                                        **ᐸEndToEndIdᐳ**BMO1 SEND PROD VER 10 1106**ᐸ/EndToEndIdᐳ** <br>
                                **ᐸ/PmtIdᐳ** <br>
                                **ᐸInstdAmt** Ccy="EUR"**ᐳ**0.20**ᐸ/InstdAmtᐳ** <br>
                                **ᐸChrgBrᐳ**SLEV**ᐸ/ChrgBrᐳ** <br>
                                **ᐸDrctDbtTxᐳ** <br>
                                        **ᐸMndtRltdInfᐳ** <br>
                                                **ᐸMndtIdᐳ**PRODVER8**ᐸ/MndtIdᐳ** <br>
                                                **ᐸDtOfSgntrᐳ**2011-10-01**ᐸ/DtOfSgntrᐳ** <br>
                                        **ᐸ/MndtRltdInfᐳ** <br>
                                **ᐸ/DrctDbtTxᐳ** <br>
                                **ᐸDbtrAgtᐳ** <br>
                                        **ᐸFinInstnIdᐳ** <br>
                                                **ᐸBICᐳ**HANDDEFF**ᐸ/BICᐳ** <br>
                                        **ᐸ/FinInstnIdᐳ** <br>
                                **ᐸ/DbtrAgtᐳ** <br>
                                **ᐸDbtrᐳ** <br>
                                        **ᐸNmᐳ**Pilot B**ᐸ/Nmᐳ** <br>
                                        **ᐸPstlAdrᐳ** <br>
                                                **ᐸCtryᐳ**NL**ᐸ/Ctryᐳ** <br>
                                        **ᐸ/PstlAdrᐳ** <br>
                                        **ᐸIdᐳ** <br>
                                                **ᐸOrgIdᐳ** <br>
                                                        **ᐸOthrᐳ** <br>
                                                                **ᐸIdᐳ**3321251633**ᐸ/Idᐳ** <br>
                                                                **ᐸSchmeNmᐳ** <br>
                                                                        **ᐸCdᐳ**CUST**ᐸ/Cdᐳ** <br>
                                                                **ᐸ/SchmeNmᐳ** <br>
                                                        **ᐸ/Othrᐳ** <br>
                                                **ᐸ/OrgIdᐳ** <br>
                                        **ᐸ/Idᐳ** <br>
                                **ᐸ/Dbtrᐳ** <br>
                                **ᐸDbtrAcctᐳ** <br>
                                        **ᐸIdᐳ** <br>
                                                **ᐸIBANᐳ**DE50514206000011350044**ᐸ/IBANᐳ** <br>
                                        **ᐸ/Idᐳ** <br>
                                **ᐸ/DbtrAcctᐳ** <br>
                                **ᐸRmtInfᐳ** <br>
                                        **ᐸUstrdᐳ**Invoice 1**ᐸ/Ustrdᐳ** <br>
                                **ᐸ/RmtInfᐳ** <br>
                        **ᐸ/DrctDbtTxInfᐳ** <br>
                        **ᐸDrctDbtTxInfᐳ** <br>
                                **ᐸPmtIdᐳ** <br>
                                        **ᐸEndToEndIdᐳ**BMO2 SEND PROD VER 11 1106**ᐸ/EndToEndIdᐳ** <br>
                                **ᐸ/PmtIdᐳ** <br>
                                **ᐸInstdAmt** Ccy="EUR"**ᐳ**0.10**ᐸ/InstdAmtᐳ** <br>
                                **ᐸChrgBrᐳ**SLEV**ᐸ/ChrgBrᐳ** <br>
                                **ᐸDrctDbtTxᐳ** <br>
                                        **ᐸMndtRltdInfᐳ** <br>
                                                **ᐸMndtIdᐳ**PRODVER9**ᐸ/MndtIdᐳ** <br>
                                                **ᐸDtOfSgntrᐳ**2011-10-01**ᐸ/DtOfSgntrᐳ** <br>
                                        **ᐸ/MndtRltdInfᐳ** <br>
                                **ᐸ/DrctDbtTxᐳ** <br>
                                **ᐸDbtrAgtᐳ** <br>
                                        **ᐸFinInstnIdᐳ** <br>
                                                **ᐸBICᐳ**HANDDEFF**ᐸ/BICᐳ** <br>
                                        **ᐸ/FinInstnIdᐳ** <br>
                                **ᐸ/DbtrAgtᐳ** <br>
                                **ᐸDbtrᐳ** <br>
                                        **ᐸNmᐳ**PILOT B**ᐸ/Nmᐳ** <br>
                                        **ᐸPstlAdrᐳ** <br>
                                                **ᐸCtryᐳ**DE**ᐸ/Ctryᐳ** <br>
                                        **ᐸ/PstlAdrᐳ** <br>
                                        **ᐸIdᐳ** <br>
                                                **ᐸOrgIdᐳ** <br>
                                                        **ᐸOthrᐳ** <br>
                                                                **ᐸIdᐳ**5567578793**ᐸ/Idᐳ** <br>
                                                                **ᐸSchmeNmᐳ** <br>
                                                                        **ᐸCdᐳ**CUST**ᐸ/Cdᐳ** <br>
                                                                **ᐸ/SchmeNmᐳ** <br>
                                                        **ᐸ/Othrᐳ** <br>
                                                **ᐸ/OrgIdᐳ** <br>
                                        **ᐸ/Idᐳ** <br>
                                **ᐸ/Dbtrᐳ** <br>
                                **ᐸDbtrAcctᐳ** <br>
                                        **ᐸIdᐳ** <br>
                                                **ᐸIBANᐳ**DE84514206000011350014**ᐸ/IBANᐳ** <br>
                                        **ᐸ/Idᐳ** <br>
                                **ᐸ/DbtrAcctᐳ** <br>
                                **ᐸRmtInfᐳ** <br>
                                        **ᐸUstrdᐳ**Invoice 2**ᐸ/Ustrdᐳ** <br>
                                **ᐸ/RmtInfᐳ** <br>
                        **ᐸ/DrctDbtTxInfᐳ** <br>
                **ᐸ/PmtInfᐳ** <br>
        **ᐸ/CstmrDrctDbtInitnᐳ** <br>
**ᐸ/Documentᐳ** <br>

</details>
## 2. Representing data structures and data types in XmAbbr and XML format data

