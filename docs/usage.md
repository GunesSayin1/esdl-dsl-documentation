# Usage

---

## Structure of the language

Every Mutation file starts by opening an ESDL file and ends with saving the variants:

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>
<span class="code-comment">
//Mutations<span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

> [!NOTE]
> ESDL file needs to be at the working directory of the application.

---

## Variables

ESDL-DSL allows setting variables by using the <code><span class="code-variable">$</span></code>sign, for Example:

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>
<span class="code-variable">
$maximumValue</span>=100.0 <span><span class="code-comment">
//Mutations<span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

Variables can be type of <code><span class="code-variable">float</span></code>,<code><span class="code-variable">string</span></code>,<code><span class="code-variable">integer</span></code>.

---

## Select

### Simple select

<code><span class="code-keyword-two">SELECT</span></code> statement is used for selecting different elements within ESDL file, for example:

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump</span> FROM <span class="code-variable">Area1</span> AS <span class="code-variable">HP</span>
<span class="code-comment">
//Mutations<span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

After <code><span class="code-keyword-two">SELECT</span></code>, an ESDL type of object needs to be specified.

Followed by <code><span class="code-keyword-two">FROM</span></code> statement, which is followed by an ID of object from ESDL file or result of another <code><span class="code-keyword-two">SELECT</span></code> statement (More about this at - [Chaining select statements](#chaining-select-statements)).

The selected object will be available for later used with the keyword specified after <code><span class="code-keyword">AS</span></code>

---

### Selecting multiple objects at once

Different object types can be stored within the same namespace, for example:

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump,PVPanel</span> FROM <span class="code-variable">Area1</span> AS <span class="code-variable">HPandPVPanel</span>
</pre></code>

---

### Selecting with condition

Conditions can be specified for <code><span class="code-keyword-two">SELECT</span></code> statements by using <code><span class="code-keyword-two">WHERE</span></code> keyword. There are three different condition operations avaiable for select statement:

- Condition by operator
- Condition with in statement
- Not connected

#### Condition by operator

Operator conditions follow standard logical comparison and compare attribute of the objects to be selected to a value. Available operators are <code><span class="code-keyword">==</span></code>,<code><span class="code-keyword">!=</span></code>,<code><span class="code-keyword">></span></code>,<code><span class="code-keyword"><</span></code>,<code><span class="code-keyword">>=</span></code>,<code><span class="code-keyword"><=</span></code>.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump,PVPanel</span> FROM <span class="code-variable">Area1</span> <span class="code-keyword">WHERE</span> <span class="code-variable">power</span> > <span class="code-variable">500</span> AS <span class="code-variable">HPandPVPanel</span>
</pre></code>

Will only select HeatPumps with higher power value than 500.

> [!NOTE]
> Name of the attribute needs to be same as the attribute name in ESDL model.

---

#### Condition with in statement

<span class="code-keyword-two">in</span> operator can be used for checking whether specific values exist in an attribute with type of <span class="code-keyword">EOrderedSet</span>. The values can be specified by using ESDL types.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">Building</span> FROM <span class="code-variable">Area1</span> <span class="code-keyword">WHERE</span> <span class="code-variable">GasHeater</span> in <span class="code-variable">asset</span> AS <span class="code-variable">BuildingsWithoutGH</span>
</pre></code>

It is also possible to use <span class="code-keyword-two">not in</span> operator to get the opposite result.

---

#### Not connected

<span class="code-keyword-two">NOT CONNECTED</span> operator can be used for filtering selected assets, and only selecting unconnected ones.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump</span> FROM <span class="code-variable">Area1</span> <span class="code-keyword">WHERE NOT CONNECTED</span> AS <span class="code-variable">unconnectedHP</span>
</pre></code>

> [!NOTE]
> The connection will be checked for ports individually. For example, an asset with only InPort connection will also be selected.

---

#### Selecting number of objects

It is possible to limit number of objects to be selected by using <span class="code-keyword-two">LAST</span> keyword.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump,PVPanel</span> FROM <span class="code-variable">Area1 </span>LAST <span class="code-variable">10</span> AS <span class="code-variable">HPandPVPanel</span>
</pre></code>

> [!NOTE]
> If the number of objects available is lower than the last variable all objects will be selected.

### Chaining select statements

Variables saved in namespace after executing a <span class="code-keyword-two">SELECT</span> statement can be used in the next statements to access deeper objects, filter, group selected objects.

For example:

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">Building</span> FROM <span class="code-variable">Area1</span> <span class="code-keyword">WHERE</span> <span class="code-variable">GasHeater</span> in <span class="code-variable">asset</span> AS <span class="code-variable">BuildingsWithoutGH</span>
<span class="code-keyword-two">SELECT</span> <span class="code-variable">EConnection</span> FROM <span class="code-variable">BuildingsWithoutGH</span> AS <span class="code-variable">EConnectionInBuildingsWithoutGH</span>
</pre></code>

## Mutations

Mutations in ESDL-DSL are used for creating different variants by performing operations on the base ESDL file.

### Add

<code><span class="code-keyword-two">ADD</span></code> mutation adds number of given assets randomly to the specified area or building. The area and building can be multiple and result of a select statement. The mutation will be executed for number of steps defined after <span class="code-keyword-two">FOR</span> keyword.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">ADD </span>FOR <span class="code-variable">5</span> STEPS:
- <span class="code-variable">10</span> <span class="code-variable">HeatPump</span> TO <span class="code-variable">Area1</span>
- <span class="code-variable">5</span> <span class="code-variable">GasHeater</span> TO <span class="code-variable">Area1</span>
<span class="code-comment">
//Mutations<span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will <span class="code-keyword-two">ADD</span> 10 HeatPumps and 5 GasHeaters TO Area1 in each step for 5 steps. The final result will have 50 HeatPumps and 25 GasHeaters added to Area1.

It is also possible to add assets into every selected area/building by using it in combination with <code><span class="code-keyword-two">SELECT</span></code>  statement.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">SELECT</span> <span class="code-variable">Area</span> FROM <span class="code-variable">TOP_AREA</span> AS <span class="code-variable">Areas</span>

<span class="code-keyword-two">ADD </span>FOR <span class="code-variable">5</span> STEPS:
- <span class="code-variable">1</span> <span class="code-variable">PVPanel</span> TO <span class="code-variable">Areas</span>
- <span class="code-variable">1</span> <span class="code-variable">ElectricityDemand</span> TO <span class="code-variable">Areas</span>
<span class="code-comment">
<span class="code-keyword">SAVE FINAL</span>
</pre></code>

### Remove
<code><span class="code-keyword-two">REMOVE</span></code> mutation can be used for either deleting number of random assets from the energy system or for deleting the selection.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">REMOVE </span>FOR <span class="code-variable">5</span> STEPS:
- <span class="code-variable">2</span> <span class="code-variable">HeatPump</span> FROM <span class="code-variable">Area1</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will remove 2 HeatPumps from Area1 in each step for total of 10 HeatPumps. 


<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">SELECT</span> <span class="code-variable">HeatPump</span> FROM <span class="code-variable">Area1</span> AS <span class="code-variable">HP_1</span>

<span class="code-keyword-two">REMOVE </span><span class="code-variable">HP_1</span>:

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will remove all the selected HeatPumps from Area1.

### Vary
<code><span class="code-keyword-two">VARY</span></code> mutation can be used for changing the attribute of an asset. The change will be applied to the given operation based on the defined operator. 

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">VARY </span>FOR <span class="code-variable">5</span> STEPS:
- <span class="code-variable">capacity</span> OF <span class="code-variable">Transformer1</span> <span class="code-variable">+1000</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>


The code will increase capacity attribute of the transformer by 1000 in each step for total of 5 steps. 

#### Available Operators

| Operator | Purpose                   | Shortcut |
|----------|---------------------------|----------|
| +        | Increases attribute       | <span class="code-keyword-two">INCREASE</span> |
| -        | Decreases attribute       | <span class="code-keyword-two">DECREASE</span> |
| *        | Multiplies attribute      | <span class="code-keyword-two">MULTIPLY</span> |
| /        | Divides attribute         | <span class="code-keyword-two">DIVIDE</span>   |
| =        | Sets attribute to a value | <span class="code-keyword-two">CHANGE</span>   |

#### Shorcuts

It is also possible to substitue available operators with shortcut clauses.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">INCREASE </span>FOR <span class="code-variable">5</span> STEPS:
- <span class="code-variable">capacity</span> OF <span class="code-variable">Transformer1</span> BY <span class="code-variable">1000</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>


The code will apply the same result with the example in the vary; increasing the capacity of the transformer by 1000 in each step for total of 5 steps.

### Connect
<code><span class="code-keyword-two">CONNECT</span></code> mutation can be used for establishing connection between two ports. The connection can be established by either defining the asset to be connected directly by ID or using <code><span class="code-keyword">CLOSEST</span></code> clause to let application find the closest asset of given type geographically.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">CONNECT </span>:
- <span class="code-variable">HP1</span> <span class="code-variable">InPort</span> TO <span class="code-variable">Transformer_1 OutPort</span>
- <span class="code-variable">ED1</span> <span class="code-variable">InPort</span> TO <span class="code-variable">CLOSEST Transformer OutPort</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will connect the <span class="code-variable">InPort</span> of <span class="code-variable">ED1</span> asset to closest <span class="code-variable">Transformer</span> type of asset's <span class="code-variable">OutPort</span> and connect <span class="code-variable">InPort</span> of <span class="code-variable">HP1</span> asset to <span class="code-variable">OutPort</span> of <span class="code-variable">Transformer_1</span> asset.

### Aggregate
<code><span class="code-keyword-two">AGGREGATE</span></code> mutation can be used for combining same type of assets into a single object. It is possible to combine the attributes of the assets by either mean or sum. 

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">AGGREGATE </span>:
- <span class="code-variable">Area1</span> <span class="code-variable">ElectricityDemand</span> USE <span class="code-variable">MEAN</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will combine all the ElectricityDemand type of assets into single asset with their asset attributes meaned.

It is also possible to group the assets by the carriers while doing the combination by using <code><span class="code-keyword-two">BY CARRIER</span></code> clause. 

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">AGGREGATE </span>:
- <span class="code-variable">Area1</span> <span class="code-variable">ElectricityDemand</span> USE <span class="code-variable">MEAN</span> <span class="code-variable">BY CARRIER</span> 

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

### Move
<code><span class="code-keyword-two">MOVE</span></code> mutation is used for moving the assets from one list to another. This operation does not change the geographical information of the asset. It is possible to move the assets directly into another area's asset list by defining ID of the assset after <code><span class="code-keyword-two">TO</span></code> clause.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">MOVE</span>:
- FROM <span class="code-variable">top_area</span> TO <span class="code-variable">AreaID</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

It is also possible to consider the geographical information of the assets to move them to the lowest or higest order of the area on that geographical point.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">MOVE</span>:
- FROM <span class="code-variable">top_area</span> TO <span class="code-variable">LOWEST</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code snippet will move the assets in Area1 to the lowest level (most nested) area on their geographical location.

### Replace
<code><span class="code-keyword-two">REPLACE</span></code> mutation can be used for replacing an area of the ESDL file. Model that will replace the defined area can be whole ESDL file or an area within another ESDL file. To specify an area within ESDL file <code><span class="code-keyword-two">FILENAME.esdl#AreaName</span></code> syntax is used.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">REPLACE</span>:
- <span class="code-variable">Area2</span> FROM <span class="code-variable">replace.esdl#Area1</span>

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

> [!NOTE]
> While performing this operation connection between areas will be deleted. References like parties and carriers of the ESDL file that will replace the area will be copied to the ESDL file to be replaced.


### Split
<code><span class="code-keyword-two">SPLIT</span></code> mutation can be used for generating individual ESDL files from areas of the ESDL file. While performing the operation, connections between areas will be deleted. Other references such as parties, carriers and control strategies will be copied.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">SPLIT</span>:

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

### Merge
<code><span class="code-keyword-two">MERGE</span></code> mutation can be used for combining areas within energy system. The operation will unionize the areas' polygons if they overlap, else it will create a multipolygon with listed area polygons. If areas doesn't have geographical reference, they will be merged into a single area without geographical reference.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">MERGE</span>:
- [<span class="code-variable">Area1</span>,<span class="code-variable">Area2</span>,<span class="code-variable">Area3</span>]
- 
<span class="code-keyword">SAVE FINAL</span>
</pre></code>

The code will merge areas Area1, Area2 and Area3 into a new area.

## If block
It is possible to use conditional logic for applying mutations by using if blocks. If block start with <code><span class="code-keyword-two">if</span></code> keyword and takes condition argument in parantheses. Currently, it is only possible to compare attribute of a single asset to a defined value. Conditional statement starts with <code><span class="code-keyword-two">assetID.attribute</span></code> syntax followed by a conditional operator such as !=, >= and ends with a defined value.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">if</span> (<span class="code-variable">assetID</span>.<span class="code-variable">attributename</span> <span class="code-variable">OPERATOR</span> <span class="code-variable">VALUE</span>){<span class="code-comment">
//Mutations</span>
}
<span class="code-keyword-two">else if</span> (<span class="code-variable">assetID</span>.<span class="code-variable">attributename</span> <span class="code-variable">OPERATOR</span> <span class="code-variable">VALUE</span>){<span class="code-comment">
//Mutations</span>
}
<span class="code-keyword-two">else</span>{<span class="code-comment">
//Mutations</span>
}

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

## Defining functions
<code><span class="code-keyword-two">DEFINE</span></code> keyword can be used to generate a new mutation by combining different mutations.

<pre v-pre data-lang="ESDL-DSL"><code>
<span class="code-keyword">OPEN</span> <span class="code-variable">base_model.esdl</span>

<span class="code-keyword-two">DEFINE</span> <span class="code-variable">ADD_AND_CONNECT</span>{
  <span class="code-keyword-two">ADD </span>FOR <span class="code-variable">1</span> STEPS:
- <span class="code-variable">10</span> <span class="code-variable">ElectricityDemand</span> TO <span class="code-variable">Area1</span> AS <span class="code-variable">ED</span>
  
- <span class="code-keyword-two">CONNECT </span>:
- <span class="code-variable">ED</span> <span class="code-variable">InPort</span> TO <span class="code-variable">CLOSEST Transformer OutPort</span>
}

<span class="code-keyword-two">ADD_AND_CONNECT </span>FOR <span class="code-variable">5</span> STEPS:

<span class="code-keyword">SAVE FINAL</span>
</pre></code>

## Saving results
It is possible to either save all mutation steps or only the final step. In order to save all steps of the mutations, the program needs to end with <span class="code-keyword">SAVE ALL STEPS</span>
</pre> clause. To only save the final step the program needs to end with <span class="code-keyword">SAVE FINAL</span>
</pre> clause.

