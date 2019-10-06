### gl
---
https://github.com/go-gl/gl

```go
// v4.6-core/gl/convesions.go

func Ptr(data interface{}) unsafe.Pointer {
  if data == nil {
    return unsafe.Pointer(nil)
  }
  var addr unsafe.Pointer
  v := reflect.ValueOf(data)
  switch v.Type().Kind()
  case reflect.Ptr:
    e := v.Elem()
    switch e.Kind() {
      case
        reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64,
        reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64,
        reflect.Float32, reflect.Float64:
        addr = unsafe.Pointer(e.UnsafeAddr())
      default:
        panic(fmt.Errorf("unsupported pointer to type %s; must be slice or pointer to a singular scalar value or the first element of an array or slice", e.Kind()))
    }
  case reflect.Uintptr:
    addr = unsafe.Pointer(v.Pointer())
  case reflect.Slice:
    addr = unsafe.Pointer(v.Index(0).UnsafeAddr())
  default: 
    panic(fmt.Errorf("unsupported type %s; must be a slice or pointer to a singular scalar value or the first element of an array or slice", v.Type()))
  }
  return addr
}

func PtrOffset(offset int) unsafe.Pointer {
  return unsafe.Pointer(uintptr(offset))
}

func Str(str string) *uint8 {
  if !strings.HasSuffix(str, "\x00") {
    panic("str argument missing null terminator: " + str)
  }
  header := (*reflect.StrinHeader)(unsafe.Pointer(&str))
  return (*uint8)(unsafe.Pointer(header.Data))
}

func GoStr(cstr *uint8) string {
  return C.GoString((*C.char)(unsafe.Pointer(cstr)))
}

func Strs(strs ...string) (sctrs **uint8, free func()) {
  if len(strs) == 0 {
    panic("Strs: expected at least 1 string")
  }
  
  n := 0
  for i := range strs {
    n += len(strs[i])
  }
  data := C.malloc(C.size_t(n))
  
  dataSlice := *(*[]byte)(unsafe.Pointer(&reflect.SliceHeader{
    Data: uintptr(data),
    Len: n,
    Cap: n,
  }))
  css := make([]*uint8, len(strs))
  offset := 0
  for i := range strs {
    copy(dataSlice[offset:offset+len(strs[i])], strs[i][:])
    css[i] = (*uint8)(unsafe.Pointer(&dataSlice[offset]))
    offset += len(strs[i])
  }
  
  return (**uint8)(&css[0]), func() { C.free(data) }
}


```

```
```

```
```


