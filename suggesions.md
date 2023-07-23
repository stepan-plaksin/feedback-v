
# Suggestions (DRAFT)
list.v (original)
```v
module collections

const (
	empty_list_size = 0
)

pub struct List {
mut:
	start &Node = unsafe { nil }
	end   &Node = unsafe { nil }
	size  int
}

struct Node {
	item int
mut:
	next &Node = unsafe { nil }
	prev &Node = unsafe { nil }
}

pub fn (list List) size() int {
	return list.size
}

pub fn (mut list List) add(value int) {
	list.size += 1
	mut node := &Node{
		item: value
	}

	if isnil(list.start) {
		list.start = node
		list.end = node

		return
	}

	mut end := list.end
	end.next = node
	node.prev = list.end
	list.end = node
}

pub fn (mut list List) remove(index int) {
	if list.size == empty_list_size || list.size <= index {
		return
	}

	mut current := list.start
	mut current_index := 0
	for !isnil(current) {
		if current_index == index {
			mut prev := current.prev
			mut next := current.next

			if !isnil(prev) {
				prev.next = next
			}
			if !isnil(next) {
				next.prev = prev
			}
			current.prev = unsafe { nil }
			current.next = unsafe { nil }

			list.size -= 1

			return
		}

		current_index += 1
		current = current.next
	}
}

pub fn (list List) for_each(do fn (int)) {
	if list.size == 0 {
		return
	}

	mut current := list.start

	for !isnil(current) {
		do(current.item)
		current = current.next
	}
}
```

list.v (with suggestions)
```v
module collections

// suggestion: using 'let' instead of 'const'
// using the following syntax is still possible
// let (
// 	EMPTY_LIST_SIZE = 0
// )
// suggestion: upper case may be used in names of constants
let EMPTY_LIST_SIZE = 0


// suggestion: using "pub" as attribute. Using 'pub struct List {...}' is still possible
[pub]
struct List {
pub:
    // suggestion: using "var" instead of "mut"
    var size  int
// suggestion: using 'inv' (invisible) keyword to make fields inaccessible from outside
inv:
    var start &Node = unsafe { nil }
    var end   &Node = unsafe { nil }
}

struct Node {
    // suggestion: by default: inv. Can be omitted
    // suggestion: using "let" instead of nothing for unmutables
    let item int
    var next &Node = unsafe { nil }
    var prev &Node = unsafe { nil }
}


// suggestion: using "pub" as attribute. Using 'pub fn size(list &List) int {...}' is still possible
[pub]
// suggestion: moving the special receiver argument to the arguments list and making no difference between functions and methods.
// Example: list.size and size(list) do the same
fn size(list &List) int { // by default: let list &List. 'let' omitted.
	return list.size
}

// suggestion: if a method put in 'List {}' then in 'var list &List' the type can be omitted. Options:
//  1. var &list
//  2. var list &
// Static functions also can be placed here. In this case no prefix is required. Just 'func doSome() { ... }'
List {
    [pub]
    fn add(var &list, value int) {
        list.size += 1

         // suggestion: using "var" instead of "mut" and using "let" instead of nothing for unmutables. No ':'
        var node = &Node {
            item: value
        }

        if isnil(list.start) {
            list.start = node
            list.end = node

            return
        }

        var end = list.end

        end.next = node
        node.prev = list.end
        list.end = node
    }
}

[pub]
fn remove(var list &List, index int) {
    if list.size == EMPTY_LIST_SIZE || list.size <= index {
        return
    }

	var current &Node = list.start
	var current_index = 0

	for !isnil(current) {
		if current_index == index {
			let prev = current.prev
			let next = current.next

			if !isnil(prev) {
				prev.next = next
			}
			if !isnil(next) {
				next.prev = prev
			}
			current.prev = unsafe { nil }
			current.next = unsafe { nil }

			list.size -= 1

			return
		}

		current_index += 1
		current = current.next
	}
}


pub fn for_each(let list &List, do fn (int)) {
	if list.size == 0 {
		return
	}

	var current = list.start

	for !isnil(current) {
		do(current.item)
		current = current.next
	}
}

```

list.v (with suggestions. No comments)
```v
module collections


let EMPTY_LIST_SIZE = 0


[pub]
struct List {
pub:
    var size  int
inv:
    var start &Node = unsafe { nil }
    var end   &Node = unsafe { nil }
}

struct Node {
    let item int
    var next &Node = unsafe { nil }
    var prev &Node = unsafe { nil }
}

[pub]
fn size(list &List) int {
	return list.size
}

List {
    [pub]
    fn add(var &list, value int) {
        list.size += 1

        var node = &Node {
            item: value
        }

        if isnil(list.start) {
            list.start = node
            list.end = node

            return
        }

        var end = list.end

        end.next = node
        node.prev = list.end
        list.end = node
    }
}

[pub]
fn remove(var list &List, index int) {
    if list.size == EMPTY_LIST_SIZE || list.size <= index {
        return
    }

	var current &Node = list.start
	var current_index = 0

	for !isnil(current) {
		if current_index == index {
			let prev = current.prev
			let next = current.next

			if !isnil(prev) {
				prev.next = next
			}
			if !isnil(next) {
				next.prev = prev
			}
			current.prev = unsafe { nil }
			current.next = unsafe { nil }

			list.size -= 1

			return
		}

		current_index += 1
		current = current.next
	}
}


pub fn for_each(let list &List, do fn (int)) {
	if list.size == 0 {
		return
	}

	var current = list.start

	for !isnil(current) {
		do(current.item)
		current = current.next
	}
}

```
