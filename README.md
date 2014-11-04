Yii-1.1-yii-input-mask
======================

Based on Robin Herbots jquery.inputmask 3.1.34 ,This extension helps the user with the input by ensuring a predefined format.

An inputmask helps the user with the input by ensuring a predefined format. This can be useful for dates, numerics, phone numbers.

#### **Highlights**:

- easy to use
- optional parts anywere in the mask
- possibility to define aliases which hide complexity
- date / datetime masks
- numeric masks
- lots of callbacks
- non-greedy masks
- many features can be enabled/disabled/configured by options
- supports readonly/disabled/dir="rtl" attributes
- support data-inputmask attribute
- multi-mask support

#### **Requirements**:

...requirements of using this extension (e.g. Yii 1.1 or above)...
Usage

- Extract the downloaded zip file and place it inside  application extensions directory.
- Default masking definitions

    - '9' : numeric
    - 'a' : alphabetical
    - '*' : alphanumeric

There are more definitions defined within the extensions. You can find info within the js-files or by further exploring the options. see - https://github.com/RobinHerbots/jquery.inputmask

#### **Static masks**:

These are the very basic of masking. The mask is defined and will not change during the input.
````php
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => '(999) 999-9999'                
            ));
````
#### **Optional masks**:

It is possible to define some parts in the mask as optional.
````php
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => "(99) 9999[9]-9999"                  
            ));
````
#### **Optional masks with greedy false**: 
````php
When defining an optional mask together with the greedy: false option, 
the inputmask will show the smallest possible mask as input first.

$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => "99999[-9999]",
                "clientOptions"=>array("greedy"=>false)   
            ));
 
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => "+44 1865 [x99999]",
                "clientOptions"=>array("greedy"=>false)   
            ));
````
#### **Dynamic masks**: 

Dynamic masks can change during the input.To define a dynamic part use { }. {n} => n repeats. {n,m} => from n to m repeats. Also {+} and {*} is allowed. + start from 1 and * start from 0.
````php
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => "aa-9{4}",
            ));
 
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => "aa-9{1,4}",
            ));
````
#### **Alternator masks**: 

The alternator syntax is like an OR statement. The mask can be one of the 2 choices specified in the alternator.

To define an alternator use the |. ex: "a|9" => a or 9 "(aaa)|(999)" => aaa or 999

Also make sure to read about the keepStatic option.
````php
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "mobile",
                "mask" => '["99.9", "X"]', /must use quotation when defining an array plugin requires this way /
                "definitions"=>array(
                    "X"=>array(
                        "validator"=>"[xX]",
                        "cardinality"=>1,
                        "casing"=>"upper"
                    )
            ));
````
#### **Define custom definitions**:

- You can define your own definitions to use in your mask. Start by choosing a masksymbol.
- validator(chrs, maskset, pos, strict, opts)

- Next define your validator. The validator can be a regular expression or a function. The return value of a validator can be true, false or a command object.
Options of the command object

- pos : position to insert, c : character to insert, caret : position of the caret, remove : position to remove, refreshFromBuffer : true => refresh validPositions from the complete buffer { start: , end: } => refresh from start to end,

-cardinality
Cardinality specifies how many characters are represented and validated for the definition.
prevalidator(chrs, maskset, pos, strict, opts)

- The prevalidator option is used to validate the characters before the definition cardinality is reached. (see 'j' example).
definitionSymbol

- When you insert or delete characters, they are only shifted when the definition type is the same. This behavior can be overridden by giving a definitionSymbol. (see example x, y, z, which can be used for ip-address masking, the validation is different, but it is allowed to shift the characters between the definitions)

````php
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "test",
                "mask" => 'y',
                "definitions" => [
                    'f'=>[
                        "validator"=>"[0-9\(\)\.\+/ ]",
                        "cardinality"=>"1",
                        "prevalidator"=>null,
                    ],
                    'g'=>[
                        "validator"=>"function (chrs, buffer, pos, strict, opts) {}",
                        "cardinality"=>1,
                        "prevalidator"=> null,
                    ],
                    'j'=>[
                        'validator'=>'[0-9\(\)\.\+/ ]',
                        'cardinality'=>4,
                        'prevalidator'=>[
                            ['validator'=>'[12]','cardinality'=>1],
                            ['validator'=>'(19|20)','cardinality'=>2],
                            ['validator'=>'(19|20)\\d','cardinality'=>3],                            
                        ],
                    ],
                    'x'=>[
                        "validator"=>"[0-2]",
                        "cardinality"=>1,
                        "definitionSymbol"=>'i'
                    ],
/*Validater as a function doesn't work please avoid doing so */
                    'y' => [
                        'validator' => 'function (chrs, buffer, pos, strict, opts) {
                                        var valExp2 = new RegExp("2[0-5]|[01][0-9]");
                                        return valExp2.test(buffer[pos - 1] + chrs);
                                    }',
                        'cardinality' => 1,
                        'definitionSymbol' => 'i'
                    ],
/*Validater as a function doesn't work please avoid doing so */
                    'z'=>[
                        "validator"=>'function (chrs, buffer, pos, strict, opts) {
                            var valExp3 = new RegExp("25[0-5]|2[0-4][0-9]|[01][0-9][0-9]");
                            return valExp3.test(buffer[pos - 2] + buffer[pos - 1] + chrs);
                        }',
                        "cardinality"=>1,
                        "definitionSymbol"=>'i'
                    ]
                    ]
            ));
````
#### **aliases**:

- With an alias you can define a complex mask definition and call it by using an alias name. So this is mainly to simplify the use of your masks. Some aliases found in the extensions are: email, currency, decimal, integer, date, datetime, dd/mm/yyyy, etc.

- First you have to create an alias definition. The alias definition can contain options for the mask, custom definitions, the mask to use etc.

- When you pass in an alias, the alias is first resolved and then the other options are applied. So you can call an alias and pass another mask to be applied over the alias. This also means that you can write aliases which "inherit" from another alias.

````php
/*date 
available date/ time aliases 
dd/mm/yyyy, mm/dd/yyyy, yyyy/mm/dd, dd.mm.yyyy, dd-mm-yyyy, mm.dd.yyyy, mm-dd-yyyy, yyyy.mm.dd, yyyy-mm-dd, datetime, hh:mm t, h:s t, hh:mm:ss, hh:mm,  mm/yyyy
*/
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "date",
                "clientOptions" => array("alias" =>  "date")
            ));
/*date */
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "date",
                "clientOptions" => array("alias" =>  "yyyy-mm-dd")
            ));
/* decimal */
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "cost",
                "clientOptions" => array(
                    "alias" =>  "decimal",
                    "groupSeparator"=>',',
                    "autoGroup"=>true
                    )
            ));
/* ip address */
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "ip_address",
                "clientOptions" => array("alias" =>  "ip"),
            ));
/* url */
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "web",
                "clientOptions" => array("alias" =>  "url"),
            ));
/* email */
$this->widget("ext.maskedInput.MaskedInput", array(
                "model" => $model,
                "attribute" => "web",
                "clientOptions" => array("alias" =>  "email"),
            ));
 ````           

### **Resources**: 
[RobinHerbots/jquery.inputmask](https://github.com/RobinHerbots/jquery.inputmask)
 
