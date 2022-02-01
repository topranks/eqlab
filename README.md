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

ALSO:

These things think they are part of a virtual-chassis mostly.  Sometimes even when 
they've been working they will revert to some weird VC state.

You might see this:
```
root@SPINE2> show virtual-chassis 

Virtual Chassis ID: ce41.58d8.21b3
Virtual Chassis Mode: Enabled
                                                Mstr           Mixed Route Neighbor List
Member ID  Status   Serial No    Model          prio  Role      Mode  Mode ID  Interface
0 (FPC 0)  NotPrsnt VM5F3D5FF6E7 vqfx-10000    
1 (FPC 1)  Inactive VM5F3D5FF6E7 vqfx-10000     128   Linecard*    N  VC
```

^^ From what I can tell the situation here is that the VM thinks it's linecard 1 of a VC, and
it's "inactive".  To fix this:

```
root@SPINE2> request virtual-chassis reactivate    
root@SPINE2:RE:1% 
root@SPINE2:RE:1% from a virtual chassis. Please make sure that no active
root@SPINE2:RE:1% o this virtual chassis has conflicting configuration.
root@SPINE2:RE:1% 
root@SPINE2:RE:1% tinue ? [yes,no] (no) yes 
root@SPINE2:RE:1% 
```

This will log you out.  Once logged out and back in you'll probably see your "xe" interfaces,
but they are all xe-1/x/x:
```
root@SPINE2> show interfaces terse 
Interface               Admin Link Proto    Local                 Remote
gr-0/0/0                up    up
pfe-1/0/0               up    up
pfe-1/0/0.16383         up    up   inet    
                                   inet6   
pfh-1/0/0               up    up
pfh-1/0/0.16383         up    up   inet    
pfh-1/0/0.16384         up    up   inet    
xe-1/0/0                up    up
xe-1/0/0.16386          up    up  
xe-1/0/1                up    up
xe-1/0/1.16386          up    up  
xe-1/0/2                up    up
xe-1/0/2.16386          up    up  
xe-1/0/3                up    up
xe-1/0/3.16386          up    up 
```

So do this to move back to xe-0/0/0 etc:
```
root@SPINE2> request virtual-chassis renumber member-id 1 new-member-id 0
```

After a few mins things should be back up working.



