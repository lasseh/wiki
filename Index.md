

## Linux
- [Linux mods](Linux/Linux)

## Network
---
### BGP
- [BGP](Network/BGP/BGP)

### OSPF
- [OSPFv3](Network/Cisco/OSPFv3)

### IPv6
- [IPv6](Network/IPv6/IPv6) 
- [IPv6-Linux](Network/IPv6/IPv6-Linux)
- [IPv6-Cisco](Network/IPv6/IPv6-Cisco)

## Windows
---
- [Win7 Mod](Windows/Win7)
- [Win7 Lid Lock](Windows/Win7-Lid-Lock)

```
func viewHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/view/"):]
    p, _ := loadPage(title)
    renderTemplate(w, "view", p)
}
```

