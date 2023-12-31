---
marp: true
theme: gaia
style: |
  @import url('../assets/css/rocky-theme.css');
  header,footer{
    color: #fff;
  }
  section header a {
    color: inherit;
  }
  section {
    padding-top: 90px;
  }
_class: lead
paginate: true
markdown.marp.enableHtml: true
header: '![w:300](../assets/rocky_logo_white.png) [Back to menu](./index.html)'
footer: '**Rocky Linux Academy > Ansible courses > 7 - Working with filters**'
---

# 7 - Working with filters

## Learning Ansible with Rocky

---
<br/>

# <i class="fa-solid fa-trophy"></i> Objectives

In this chapter you will learn how to transform data with jinja filters.

:heavy_check_mark: Transform data structures as dictionaries or lists;  
:heavy_check_mark: Transform variables.

---
<br/>

# Plan

* [Converting data](#9)


---
#
<br/>
<br/>
<br/>

We have already had the opportunity, during the previous chapters, to use the jinja filters.

These filters, written in python, allow us to manipulate and transform our ansible variables.

---
<style scoped>
code {
  font-size: 0.5em;
}
</style>

Throughout this chapter, we will use the following playbook to test the different filters presented:

<div class="columns">
<div>

```
- name: Manipulating the data
  hosts: localhost
  gather_facts: false
  vars:
    zero: 0
    zero_string: "0"
    non_zero: 4
    true_booleen: True
    true_non_booleen: "True"
    false_boolean: False
    false_non_boolean: "False"
    whatever: "It's false!"
    user_name: antoine
    my_dictionary:
      key1: value1
      key2: value2
```

</div>
<div>


```
    my_simple_list:
      - value_list_1
      - value_list_2
      - value_list_3
    my_simple_list_2:
      - value_list_3
      - value_list_4
      - value_list_5
    my_list:
      - element: element1
        value: value1
      - element: element2
        value: value2

  tasks:
    - name: Print an integer
      debug:
        var: zero
```

</div>
</div>

---
#

> The following is a non-exhaustive list of filters that you are most likely to encounter or need.
    Fortunately, there are many others. You could even write your own!

---
#

The playbook will be played as follows:

```
ansible-playbook play-filter.yml
```

---
#

<br/>
<br/>
<br/>

> More information can be found at https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html.

---
<br/>
<br/>
<br/>

# Converting data

---
# Converting data

Data can be converted from one type to another.

To know the type of a data (the type in python language), you have to use the `type_debug` filter.

---
#

Example:

```
- name: Display the type of a variable
  debug:
    var: true_boolean|type_debug
```

which gives us:

```
TASK [Display the type of a variable] ******************************************************************
ok: [localhost] => {
    "true_boolean|type_debug": "bool"
}
```

---
#

It is possible to transform an integer into a string:

```
- name: Transforming a variable type
  debug:
    var: zero|string
```

```
TASK [Transforming a variable type] ***************************************************************
ok: [localhost] => {
    "zero|string": "0"
}
```

---
#

Transform a string into an integer:

```
- name: Transforming a variable type
  debug:
    var: zero_string|int
```

---
<style scoped>
code {
  font-size: 0.5em;
}
</style>
#

or a variable into a boolean:

```
- name: Display an integer as a boolean
  debug:
    var: non_zero | bool

- name: Display a string as a boolean
  debug:
    var: true_non_boolean | bool

- name: Display a string as a boolean
  debug:
    var: false_non_boolean | bool

- name: Display a string as a boolean
  debug:
    var: whatever | bool

```

---
<style scoped>
code {
  font-size: 0.4em;
}
</style>
A character string can be transformed into upper or lower case:

```
- name: Lowercase a string of characters
  debug:
    var: whatever | lower

- name: Upercase a string of characters
  debug:
    var: whatever | upper
```

which gives us:

```
TASK [Lowercase a string of characters] *****************************************************
ok: [localhost] => {
    "whatever | lower": "it's false!"
}

TASK [Upercase a string of characters] *****************************************************
ok: [localhost] => {
    "whatever | upper": "IT'S FALSE!"
}
```

---
#

The `replace` filter allows you to replace characters by others.


---
<style scoped>
code {
  font-size: 0.4em;
}
</style>
Here we remove spaces or even replace a word:

```
- name: Replace a character in a string
  debug:
    var: whatever | replace(" ", "")

- name: Replace a word in a string
  debug:
    var: whatever | replace("false", "true")
```

which gives us:

```
TASK [Replace a character in a string] *****************************************************
ok: [localhost] => {
    "whatever | replace(\" \", \"\")": "It'sfalse!"
}

TASK [Replace a word in a string] *****************************************************
ok: [localhost] => {
    "whatever | replace(\"false\", \"true\")": "It's true !"
}
```

---
#

The `split` filter splits a string into a list based on a character:

```
- name: Cutting a string of characters
  debug:
    var: whatever | split(" ", "")
```


```
TASK [Cutting a string of characters] *****************************************************
ok: [localhost] => {
    "whatever | split(\" \")": [
        "It's",
        "false!"
    ]
}
```

---
# Join the elements of a list

It is frequent to have to join the different elements in a single string.
We can then specify a character or a string to insert between each element.

---
<style scoped>
code {
  font-size: 0.5em;
}
</style>

# 
```
- name: Joining elements of a list
  debug:
    var: my_simple_list|join(",")

- name: Joining elements of a list
  debug:
    var: my_simple_list|join(" | ")
```

which gives us:

```
TASK [Joining elements of a list] *****************************************************************
ok: [localhost] => {
    "my_simple_list|join(\",\")": "value_list_1,value_list_2,value_list_3"
}

TASK [Joining elements of a list] *****************************************************************
ok: [localhost] => {
    "my_simple_list|join(\" | \")": "value_list_1 | value_list_2 | value_list_3"
}
```

---
# Transforming dictionaries into lists (and vice versa)

The filters `dict2items` and `itemstodict`, a bit more complex to implement, are frequently used, especially in loops.

---
<style scoped>
code {
  font-size: 0.5em;
}
</style>

Note that it is possible to specify the name of the key and of the value to use in the transformation.

```
- name: Display a dictionary
  debug:
    var: my_dictionary

- name: Transforming a dictionary into a list
  debug:
    var: my_dictionary | dict2items

- name: Transforming a dictionary into a list
  debug:
    var: my_dictionary | dict2items(key_name='key', value_name='value')

- name: Transforming a list into a dictionary
  debug:
    var: my_list | items2dict(key_name='element', value_name='value')
```

---

<div class="columns">
<div>

```
TASK [Display a dictionary] ******************************
ok: [localhost] => {
    "my_dictionary": {
        "key1": "value1",
        "key2": "value2"
    }
}

TASK [Transforming a dictionary into a list] *************
ok: [localhost] => {
    "my_dictionary | dict2items": [
        {
            "key": "key1",
            "value": "value1"
        },
        {
            "key": "key2",
            "value": "value2"
        }
    ]
}
```

</div>
<div>

```
TASK [Transforming a dictionary into a list] ******************************
ok: [localhost] => {
    "my_dictionary | dict2items (key_name = 'key', value_name = 'value')": [
        {
            "key": "key1",
            "value": "value1"
        },
        {
            "key": "key2",
            "value": "value2"
        }
    ]
}

TASK [Transforming a list into a dictionary] ******************************
ok: [localhost] => {
    "my_list | items2dict(key_name='element', value_name='value')": {
        "element1": "value1",
        "element2": "value2"
    }
}
```

</div>
</div>


---
<style scoped>
code {
  font-size: 0.5em;
}
</style>
# Working with lists

It is possible to merge or filter data from one or more lists:

```
- name: Merger of two lists
  debug:
    var: my_simple_list | union(my_simple_list_2)
```

```
ok: [localhost] => {
    "my_simple_list | union(my_simple_list_2)": [
        "value_list_1",
        "value_list_2",
        "value_list_3",
        "value_list_4",
        "value_list_5"
    ]
}
```

---
#

To keep only the intersection of the 2 lists (the values present in the 2 lists):

```
- name: Merger of two lists
  debug:
    var: my_simple_list | intersect(my_simple_list_2)
```

```
TASK [Merger of two lists] *********************************************
ok: [localhost] => {
    "my_simple_list | intersect(my_simple_list_2)": [
        "value_list_3"
    ]
}
```

---
#

Or on the contrary keep only the difference (the values that do not exist in the second list):

```
- name: Merger of two lists
  debug:
    var: my_simple_list | difference(my_simple_list_2)
```

```
TASK [Merger of two lists] ***************************************************
ok: [localhost] => {
    "my_simple_list | difference(my_simple_list_2)": [
        "value_list_1",
        "value_list_2",
    ]
}
```

---
# 

If your list contains non-unique values, it is also possible to filter them with the `unique` filter.

```
- name: Unique value in a list
  debug:
    var: my_simple_list | unique
```

---
# Transformation json/yaml

You may have to import json data (from an API for example) or export data in yaml or json.

```
- name: Display a variable in yaml
  debug:
    var: my_list | to_nice_yaml(indent=4)

- name: Display a variable in json
  debug:
    var: my_list | to_nice_json(indent=4)
```

---
#

```
TASK [Display a variable in yaml] *************************************************************
ok: [localhost] => {
    "my_list | to_nice_yaml(indent=4)": "-   element: element1\n    value: value1\n-   element: element2\n    value: value2\n"
}

TASK [Display a variable in json] *************************************************************
ok: [localhost] => {
    "my_list | to_nice_json(indent=4)": "[\n    {\n        \"element\": \"element1\",\n        \"value\": \"value1\"\n    },\n
        {\n        \"element\": \"element2\",\n        \"value\": \"value2\"\n    }\n]"
}
```

---
# Default values, optional variables, protect variables

You will quickly be confronted with errors in the execution of your playbooks if you do not provide default values for your variables, or if you do not protect them.

---
#

The value of a variable can be substituted for another one if it does not exist with the `default` filter:

```
- name: Default value
  debug:
    var: variablethatdoesnotexists | default(whatever)
```

```
TASK [Default value] *******************************************************
ok: [localhost] => {
    "variablethatdoesnotexists | default(whatever)": "It's false!"
}
```

---
#

Note the presence of the apostrophe `'` which should be protected, for example, if you were using the `shell` module:

```
- name: Default value
  debug:
    var: variablethatdoesnotexists | default(whatever| quote)
```

```
TASK [Default value] ***********************************************************
ok: [localhost] => {
    "variablethatdoesnotexists | default(whatever|quote)": "'It'\"'\"'s false!'"
}
```

---
#

Finally, an optional variable in a module can be ignored if it does not exist with the keyword `omit` in the `default` filter, which will save you an error at runtime.

```
- name: Add a new user
  ansible.builtin.user:
    name: "{{ user_name }}"
    comment: "{{ user_comment | default(omit) }}"
```

---
# Associate a value according to another one (`ternary`)

Sometimes you need to use a condition to assign a value to a variable, in which case it is common to go through a `set_fact` step.

This can be avoided by using the `ternary` filter.

---
#

```
- name: Default value
  debug:
    var: (user_name == 'antoine') | ternary('admin', 'normal_user')
```

```
TASK [Default value] ********************************************************************************
ok: [localhost] => {
    "(user_name == 'antoine') | ternary('admin', 'normal_user')": "admin"
}
```

---
<style scoped>
code {
  font-size: 0.7em;
}
</style>
# Some other filters

  * `{{ 10000 | random }}`: as its name indicates, gives a random value.
  * `{{ my_simple_list | first }}`: extracts the first element of the list.
  * `{{ my_simple_list | length }}`: gives the length (of a list or a string).
  * `{{ ip_list | ansible.netcommon.ipv4 }}`: only displays v4 IPs. Without dwelling on this, if you need, there are many filters dedicated to the network.
  * `{{ user_password | password_hash('sha512') }}`: generates a hashed password in sha512.


---
<br/>
<br/>
<br/>


# Questions ?

---
#

<div class="columns">
<div>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

[Index](./Learning_Ansible_with_Rocky-0-Introduction.html)


</div>
<div>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

[Next Chapter >>](./Learning_Ansible_with_Rocky-8-Ansible_Management_server_optimizations.html)

</div>
</div>