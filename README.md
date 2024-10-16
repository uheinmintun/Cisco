<table>
    <tr>
        <td>R1</td>
        <td>R2</td>
    </tr>
    <tr>
<td>

```py
host R1

int e0/0
ip add 13.0.0.1 255.255.255.0
no shut
int e0/1
ip add 192.168.0.1 255.255.255.0
no shut

ip route 0.0.0.0 0.0.0.0 13.0.0.3
```
</td>
<td>

```py
host R2

int e0/1
ip add 23.0.0.2 255.255.255.0
no shut
int e0/0
ip add 172.16.0.2 255.255.255.0
no shut

ip route 0.0.0.0 0.0.0.0 23.0.0.3
```

</td>
    </tr>
</table>