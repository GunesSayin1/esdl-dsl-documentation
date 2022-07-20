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

<span class="code-keyword-two">ADD</span> mutation adds number of given assets randomly to the specified area or building. The area and building can be multiple and result of a select statement. The mutation will be executed for number of steps defined after <span class="code-keyword-two">FOR</span> keyword.

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

### Remove

### Vary

#### Available Operators

#### Shorcuts

### Connect

### Aggregate

### Move

### Replace

### Split

### Merge

## Defining custom mutation

## If block

## Saving results

<pre v-pre data-lang="ESDL-DSL"><code>
SELECT <span class="code-keyword">assetType</span> FROM <span class="code-keyword">Area</span>
</code></pre>
