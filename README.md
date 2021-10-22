# eqlab
Homer config files and templates for vQFX EVPN testing

![gns3 topology](https://github.com/topranks/eqlab/raw/main/gns3_layout.png)

Tips:

Start vQFX RE nodes first, give them a few mins to boot.  Then start the PFE nodes.

Sometimes the vQFX doesn't work properly if you start them both simultaneously.

Run "show interface terse" to get status of PFE.  Once PFE is up and connected the
"em" interfaces should be mapped to "xe-" ones.  Generally this means things are ok,
but sometimes even when the interfaces are detected traffic doesn't flow.

Starting PFE after RE generally prevents this, but if it happens reboot PFE of 
affected node.

