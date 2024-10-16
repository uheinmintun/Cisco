<table>
    <tr>
        <td>R1</td>
        <td>R2</td>
    </tr>
    <tr>
        <td>
            host R1<br>int e0/0<br>ip add 13.0.0.1 255.255.255.0<br>no shut<br>int e0/1<br>ip add 192.168.0.1 255.255.255.0<br>no shut<br>ip route 0.0.0.0 0.0.0.0 13.0.0.3
        </td>
        <td>
        ```
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

<table>
    <tr>
        <td>R1</td>
        <td>R2</td>
    </tr>
    <tr>
        <td>```py<br>host R1<br>int e0/0<br>ip add 13.0.0.1 255.255.255.0<br>no shut<br>int e0/1<br>ip add 192.168.0.1 255.255.255.0<br>no shut<br>ip route 0.0.0.0 0.0.0.0 13.0.0.3<br>```</td>
        <td>```py<br>host R2<br>int e0/1<br>ip add 23.0.0.2 255.255.255.0<br>no shut<br>int e0/0<br>ip add 172.16.0.2 255.255.255.0<br>no shut<br>ip route 0.0.0.0 0.0.0.0 23.0.0.3<br>```</td>
    </tr>
</table>

<table>
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

            ```
            Hello
            ```
</td>
</tr>
<tr>
<td>

</td>
<td>

</td>
</tr>
</table>