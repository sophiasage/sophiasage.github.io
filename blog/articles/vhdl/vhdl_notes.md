VHDL Coding Notes
---------------------------


Vocabulary
------------------
  - register-transfer level (RTL) is a design abstraction which models a 
    synchronous digital circuit in terms of data between hardware registers,
    and the logical operations performed on those signals.

  - synchronous digital circuit: the changes in the state of memory element
    are synchronized by a clock signal. 

  - GPIO : general purpose input/output. Used to control or monitor circuitry 
    on a board, enabling/diabling power to other circuitry, reading states of 
    on-board switches, driving LED status indicators.

  - CLB : configurable logic block. Made of logical elements grouped together
    in a slice
 
  - LUT : look-up table. combinatorial logic based on input combos- gives 
    gives specific output.

Coding
------------------

Elements of VHDL:

1. Entity - used to specify the input and output ports of the circuit
 
    Declaration:

    ```vhdl
    entity entity_name is 
    port(
         port_1_name : mode data_type;
         ....
         port_n_name : mode data_type;
    );
    ```
    
    Rules for writing port names:
      - Port name consists of letters, digits, and underscores.
      - It always begins with a letter
      - not case sensitive
    
    Modes of Port
      - in - input port
      - out - output port
      - inout - bidirectional port
      - buffer - buffered output port

2. Architecture

    The actual description of the design which is used to describe how the circuit
    operates. Can contain both concurrent and sequential statements.
    
    Declaration:
    
    ```vhdl
    architecture architecture_name of entity_name is
    begin
        (concurrent statements)
    end architecture_name;
    ```

    Architectures can also have components. 
    ```vhdl
    architecture Behavioral of top_module is
    component sub module
    port(x,y : in STD_LOGIC;
    s: in STD_LOGIC;
    z: out STD_LOGIC);
    end component;

    begin
    sub_module port map(
    	A => x,  -- here A,B,S0 are ports of the top module
	B => y,
	S0 => s,
	m1 =>z);
    end Behavioral;
    ```

3. Configuration - defines how the design hierarchy is linked together. 
   Associates architectures with entities.

    Declaration:
    
    ```vhdl
    configuration configuration_name of entity_name is 
        for architecture_name
            for instance_label : component_name
                use entity library_name.entity_name(archtecture_name);
            end for;
        end for;
    end configuration;
    ```

VHDL Objects:
-------------

   1. `constant constant_name : type := initial_value ;`
      `constant number_of_bytes : integer := 8;`
   
   2. `variable index : integer := 0 `
      Note: if you don't define a range for integers, it will default to the max
      range.
      `signalA : integer range 0 to 7;`  

      Variables are placeholders for signals. They take on value immediately, 
      whereas signals take on value when the process suspends. They must be 
      declared and then assigned. They are local to the process where they are 
      declared.

 
   3. `signal sig1 : std_logic ; sig1 <= '1'`

Data Types
----------

  scalars: integer, floating point, enumeration, physical
  composite: arrays, records

  Enumerated types:
    `type <new type name> is ( <list of items> );`
    Caution: types enclosed in single quotes are case sensitive

  the `std_logic` type can take on the following values:
    - 'U' - uninitialized
    - 'X' - forcing unknown
    - '0' - forcing zero
    - '1' - forcing one
    - 'Z' - high impedence
    - 'W' - weak unknown
    - 'L' - weak zero
    - 'H' - weak one
    - '-' - don't care
  warning: these types must be capitalized.


Composite Type
==============

example: `signal A_word : std_logic_vector (3 downto 0) := "0011" ;`

  - no predefined LSB/MSB interpretation, so "0011" does not necessarily mean 3
  - double quotes are used for any bit vector, `std_logic_vector`, or string object
  - vectors are indexed from left to right
  - it's good coding practice to pick an ordering (downto) and stick with it

signed and unsigned : arrays of type `std_logic`
  - have positional meaning
  - left-most bit is a sign
  - signed 0110 is 6
  - unsigned 1110 is 14
  - use unsigned when you need to do math
  - you can't perform math with `std_logic`

Arrays are groups of elements all of the same type

syntax:

`type <new type name> is array (<range>) of data_type;`
here range specifies the min and max values of the indies via to or downto
example:
`type WORD is array (0 to 3) of std_logic;`  [0 | 1 | 2 | 3 ] 
initializing the type:

Example1: 

`signal my_sig : WORD ;`

Example2:

`type string is array (positive range <> ) of character;`
`constant msg: string(1 to 10) := "setup time";`


Data Type Conversion
--------------------

  - either casting or through a conversion function
  - used to move between `std_logic_vector` and signed and unsigned types
  - original and destination signals must have the same bit width

Moving from `std_logic_vector` to integer:
  - cast `std_logic_vector` to signed or unsigned
  - `integer_value <= to_integer(unsigned(slv_vector));`
 
Moving from integer to `std_logic_vector`:
  - `slv_value <= std_logic_vector (to_unsigned(integer_value, n ))`



Logical Operators
-----------------

  and, or, nand, nor, xor, xnor, not

Relational Operators
--------------------

  = , /= , < , > , <= , >= 

Arithmetic 
-----------

  + , - , * , / , & , mod, rem , abs, **

Shift Operators
----------------

  - sll shift logcial left
  - srl shift logical right
  - sla shift arithmetic left
  - sra shift arithmetic right
  - rol rotate left
  - ror rotate right


Conditional Statements
-----------------------
Usually containted inside of a process statement

- if/else syntax:
=================

```vhdl
process
begin 
if (boolean expression 1) then
    sequential statements;
elsif (boolean exp. 2) then
    sequential staements;
else
    sequential statements;
end if;
```

- when/else is used for concurrent statements

```vhdl
architecture...
  begin
    Z<= A when Sel = "00" else
        B when Sel = "11" else
        'X';
end archtiecture;
```

Rules:

  - first condition found to be true will be executed
  - if/elsif implies priority among conditions. first condition - highest priority.

- Case Statement
================

Allows operations to be performed based on the value of the selector expression

Syntax:

```vhdl
case (<selector expression>) is
  when <target> => <sequential statement>;
  ...
  when others => <sequential statements>;
end case;
```
Example : create a MUX:
```vhdl
process (A,B,C,D,SEL)
begin
case SEL is 
  when "00" => Z <= A;
  when "01" => Z <= B;
end case;
...
end process;
```

Rules:
  - all possible values of the selector expression must be specified
  - no choices may overlap
  - ranges by not be used as targets
  - only allwoed in process, function, or procedure
  - can use "when others" keyword to catch conditions not specified


Processes
------------------

VHDL code is built of processes. Only signals carry values from one process to
another. 
Processes execute concurrently, but statements inside processes execute sequentially.

syntax:
```vhdl
<label> : process ( <sensitivity list> ) 
    <process declarative area>
begin 
    <process behavior area>
end process <label>;
```
Note: it is optional to name the process with a label.

Best Practices:

   - use synchronous over asynchronous processes
   - use flip-flops built into Xilinx architecture along with combinatorial resources
   - avoid resets 
   - give a meaningful name to each process


Misc
------------------
multiline comment: /* ...*/


Test Bench
------------------

Port map: top module variables => component variables
