# terraform

## for

### Looping over a list of maps

```hcl
locals {
  virtual_machines = {
    linux = [
      {
        name = "lin_vm_a"
        size = "Standard_B4ms"
      },
      {
        name = "lin_vm_b"
        size = "Standard_D4_v2"
      }
    ]
  }
}

resource "local_file" "foo" {
    for_each = { for index, vm in local.virtual_machines.linux: vm.name => vm }

    content  = "foo"
    filename = "${each.value.name} / ${each.value.size}"
}
```

Will produce.

```plaintext
Terraform will perform the following actions:

  # local_file.foo["lin_vm_a"] will be created
  + resource "local_file" "foo" {
      + content              = "foo"
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "lin_vm_a / Standard_B4ms"
      + id                   = (known after apply)
    }

  # local_file.foo["lin_vm_b"] will be created
  + resource "local_file" "foo" {
      + content              = "foo"
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "lin_vm_b / Standard_D4_v2"
      + id                   = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```


```hcl
locals {
  virtual_machines = {
    linux = [
      {
        size = "Standard_B4ms"
        name = "lin_vm_a"
      },
      {
        size = "Standard_D4_v2"
        name = "lin_vm_b"
      }
    ]
  }
}

resource "local_file" "foo" {
    for_each = { for vm in local.virtual_machines.linux: vm.name => vm }

    content  = "foo"
    filename = "${each.key} / ${each.value.size} / ${each.value.name}"
}
```

Will produce.

```plaintext
Terraform will perform the following actions:

  # local_file.foo["lin_vm_a"] will be created
  + resource "local_file" "foo" {
      + content              = "foo"
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "lin_vm_a / Standard_B4ms / lin_vm_a"
      + id                   = (known after apply)
    }

  # local_file.foo["lin_vm_b"] will be created
  + resource "local_file" "foo" {
      + content              = "foo"
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "lin_vm_b / Standard_D4_v2 / lin_vm_b"
      + id                   = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```
