
# yaml:

a: 'hello'
b: 234
c: 2.345
d:
  - 1
  - 2
  - 3


# xml:
<root>
  <a>hello</a>
  <b>234</b>
  <c>2.345</c>
  <d>
    <value>1</value>
    <value>2</value>
    <value>3</value>
  </d>
</root>

# dtd:
#
<xs:element name="root">

    <xs:complexType>
        <xs:sequence>
        <xs:element name="a" type="xs:string"/>
        <xs:element name="b" type="xs:integer"/>
        <xs:element name="c" type="xs:float"/>
        <xs:element name="d" type="xs:array"/>
        </xs:sequence>
    </xs:complexType>

</xs:element>



