---
title: Hex2Int
date: 2016-05-17 15:38:29
tags:
---
___十六进制字符串转成整数___
```c
unsigned int HEX2int(NSString  * hexString)
{
    unsigned int nReval = 0;
    int nPower = 1;
    size_t nStrlen = hexString.length;

    while(nStrlen--)
    {
        unichar ch=[hexString characterAtIndex:nStrlen];
        if(isdigit(ch) == 0)
        {
            ch = ch&0x4f;
            nReval += (ch - 55)* nPower;
        }
        else
        {
            nReval += (ch - '0')* nPower;
        }
        nPower *= 16;
    }
    return nReval;
}
```
