# Debugging variables in Ansible
Sometimes it's useful to debug where and how your playbook variables are defined. Your variables might be returning unexpected values or variable precedence[1] is playing tricks on you.

This playbook shows how to use three hidden YAML attributes (`_line_number`, `_column_number` and `_data_source`)[2] to debug the origin of your playbook variables.

As an example we have defined the following variables in our example playbook `debugging-variables.yml`:

`var1` is defined using the `vars` Play directive  
`var1` is also defined in the inventory.. Precendence ;-)  
`var2` is defined using the `vars_files` Play directive  
`var3` is defined in `host_vars/localhost.yml`  
`var4` is defined in `group_vars/all.yml`  
`var5` is defined using the `include_vars` module  
`var6` is defined in the inventory  

**References:**  
*[1] Ansible Docs on variable precedence: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable*  
*[2] Nitty gritty details on how these attributes are set inside Ansible: https://github.com/ansible/ansible/blob/devel/lib/ansible/parsing/yaml/objects.py.*

# Running the playbook
Use the `ansible-playbook` command to run the included playbook:

`$ ansible-playbook debugging-variables.yml`

Should result in the following:
```
ok: [localhost] => (item=var1) => {
    "item": "var1",
    "var1, var1.__dict__": "(u'value1', {'_column_number': 11, '_line_number': 9,
    '_data_source': u'/Dev/project/debugging-variables.yml'})"
}
...
```

# Example usage
How to access these hidden variable attributes inside a playbook or template:
```
Variable 'var1' has the value '{{ var1 }}', as defined on line {{ var1._line_number }},
column {{ var1._column_number }} in '{{ var1._data_source }}'.
```

Which will return the following information:
```
Variable 'var1' has the value 'value1', as defined on line 9, column 11 in '/Dev/project/playbook.yml'.
```

Verify in `/Dev/project/playbook.yml`:
```
 7: 
 8:  vars:
 9:    var1: value1 <-- line 9
10:          ^-- column 11
11:
```
