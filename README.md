# **XmAbbr (XML Abbreviated)**

## Introduction

XmAbbr is a lightweight abbreviated alternative serialisation language for XML (version 1.1) formatted data and can represent any standalone XML file. In addition the language specifies a computer language independent XML representation of structured typed data together with a series of mapping conventions for use with specific computer languages and applications.

XmAbbr is intended to be easier to read and edit (without needing special tools) than XML plus also facilitating standardised methods for representing structured data for direct import and export from a broad range of applications and computer languages. Anticipated uses range from messaging between applications to configuration files and simplified export or import of XML files.

Conversion to and from between XML and XmAbbr formatted data results in no semantic loss of information. In addition XmAbbr serves as an object notation – for example a round trip conversion between JSON formatted data and XmAbbr (ie: from JSON to XmAbbr and back again) also results in no loss of semantic information.

The XmAbbr format has a strong visual resemblance to YAML and is intended to be presented in an indented style though (as with XML) it does not use indentation as a markup element. Where used to convey data type information a form of “Duck Typing” (inferred data type) is employed. For XmAbbr data programmatically generated this is designed to be unambiguous and the rules for avoiding confusion between text and typed data in the case of manual editing are simple and clear.

This document is not a specification but where used the capitalised words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and       "OPTIONAL" have the meanings as described in RFC 2119. Where definitions of grammar are given these use an Extended Backus-Naur Form (EBNF) notation.

XmAbbr has three distinct constituent parts:

1. An alternate markup language for representing XML data which fully covers all features of XML except for external DTDs (Document Type Definitions) and Schemas which alter the XML content (All XML files generated from XmAbbr content have the XML declaration attribute standalone = "yes"). One aim of the language (as far as possible) is for most people to be able to infer from an example what the basic structure and meaning of this is.

2. A definition of how high level data constructs (such as objects, arrays and data types) are represented and interpreted within XML structured data. So, while XmAbbr can be used for any XML data, in the case of structured data a subset of XML is used. This also includes some naming and layout conventions for XML elements and attributes.

3. Specific definitions for various computer languages and environments as to how structured XML / XmAbbr data is to be both interpreted and generated. Of these JavaScript is the main target and XmAbbr is able to fulfil the same functions as JSON (eg: direct import and export of JavaScript objects). Data in the JSON format can be converted to the XML / XmAbbr format and converted back again without loss of information.

## 1. Representing XML format data in XmAbbr

The two types of content in XmAbbr (as with XML) are mark up and data. Mark up is contained in markup tags or the use of quoted text and is identified by four markup character definitions. These are a space or non printinable character, “:”, ‘”’ (Double Quote) and “’” (Single Quote or Apostrophe). These have a meaning analogous to the use of “<”, “>”, ‘”’ and “’” in XML. A markup tag is a sequence of characters enclosed by a leading white space character and a trailing “:”.  Quoted text is a sequence of characters enclosed within matched ‘”’ or “’” characters. 

The basic form of a markup tag is one or more optional control characters followed by a property name (optional). The essential control symbols used are “-” to indicate down one level and “+” (one or more) to indicate up one or more levels. The following example is of data for an element called peopleList containing elements called name which in turn contains elements named title, firstName, middlename, lastName, suffix and gender.

${\color{blue}peopleList:}$  <br>
          ${\color{blue}\-name:}$  <br>
                    ${\color{blue}\-title: \space \color{red}Mr \space \space \space \space \color{blue}firstName: \space \color{red}John \space \space \space \space \space \space \color{blue}middleName: \space \color{red}Albert\space \space \space  \space \color{blue}lastName: \space \color{red}Doe \space \space \space \space \space \color{blue}gender: \space \color{red}Male}$  <br>
          ${\color{blue}\+name:}$  <br>
                    ${\color{blue}-title: \space \color{red}Ms \space \space \space \space \color{blue}firstName: \space \color{red}Edith \space \space \space \space \space \color{blue}middleName: \space \color{red}Jane Lousie \space \space \space  \space \color{blue}lastName: \space \color{red}Pargetter \space \space \space \space \space \color{blue}gender: \space \color{red}Female}$  <br>
<br>
*(Markup tags are shown in blue and data in red)* <br>
Conversion to XML

${\color{blue}peopleList}$  <br>
          ${\color{blue}{'\u003E'}{'\u003C'}name}$  <br>
                    ${\color{blue}\>\<title\> \color{red}Mr \color{blue}\</title\>\<firstName> \color{red}John \color{blue} \></firstName}$ <br>                                                                                                                  
        
### 1.1 Summary of markup notation 
<details>
<summary>   Details </summary>

Before processing any XmAbbr input data is normalised to Unicode NFC (Normalisation Form Canonical Composition). This is however applied before any numeric character references are decoded. Any space or non-printable character is treated as a word or token delimiter

#### 1.1.1 Property Name rules

Property names are either standard names or quoted text (QuotedText). A standard name follows the form common in most computer languages of variable names starting with a visible character which is not a symbol or ASCII decimal digit (0–9) with digits allowed for following characters. In addition the underline (“_”) character is allowed as both a starting and following character while  hyphen minus (“-”) can be a following character. Within XML the additional characters of “:” and “.” are allowed within property names but are used for specific purposes (such as namespaces) and are not allowed in XmAbbr standard names.
 
<details>
<summary> EBNF definition </summary>

NonPrintingChar ::=  #x20 |[#x00-#x19] #x7F-#x9F| [#x2000-#x200A] |#x2028 | #x2029 | #x202F | #x205F | #x3000 
SeparatorSpaces ::= ( NonPrintingChar )+

SingleQuotedText ::= "'" ( [^'] | "''" )* "'"
DoubleQuotedText ::= '"' ( [^"] | '""' )* '"'
QuotedText ::= SingleQuotedText | DoubleQuotedText


TokenisedString ::= SeparatorSpaces 
TokenisedText ::= SeparatorSpaces 


NameStartChar ::= ":" | [A-Z] | "_" | [a-z] | [#xC0-#xD6] | [#xD8-#xF6] | [#xF8-#x2FF] | [#x370-#x37D] | [#x37F-#x1FFF] | [#x200C-#x200D] | [#x2070-#x218F] | [#x2C00-#x2FEF] | [#x3001-#xD7FF] | [#xF900-#xFDCF] | [#xFDF0-#xFFFD] | [#x10000-#xEFFFF]
NameChar ::= NameStartChar | "-" | "." | [0-9] | #xB7 | [#x0300-#x036F] | [#x203F-#x2040]
Name ::= NameStartChar ( NameChar )*
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

Insert text here

</details>
## 2. Representing data structures and data types in XmAbbr and XML format data

