
# Forensics

This is a cheatsheet on some of the tools I use for forensics / Blue Team challenges in CTFs

## Linux

### Malware Analysis
[Remnux](https://docs.remnux.org/)

## Windows

### Malware Analysis

[BlobRunner](https://github.com/OALabs/BlobRunner)

[Mandiant - flare-vm](https://github.com/mandiant/flare-vm)

[Malware Archaeology - Cheat Sheet](https://www.malwarearchaeology.com/cheat-sheets)

### Event Logs

[WithSecureLabs/chainsaw](https://github.com/WithSecureLabs/chainsaw)

[omerbenamram/evtx](https://github.com/omerbenamram/evtx)

```bash
# Outputs to script_output.txt | File has XML in it
for file in /home/kali/Downloads/forensics/Windows/System32/Winevt/Logs/*
do
  ../evtx_dump-v0.8.1-x86_64-unknown-linux-musl "$file" >> script_output.txt
done
```

### Visual Basic Scripts

[decalage2/ViperMonkey](https://github.com/decalage2/ViperMonkey)
