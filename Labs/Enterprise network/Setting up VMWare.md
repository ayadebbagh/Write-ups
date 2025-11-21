### Configuring the VMNet8 NAT Network

After installing VMware Workstation, we configured the default **VMNet8 NAT network** to ensure our virtual machines could communicate with each other while maintaining Internet access. By default, VMNet8 comes with standard NAT settings, but we modified the network to align with our project’s IP address scheme.

**Steps:**

1. Open VMware Workstation and navigate to Edit → Virtual Network Editor.
2. Click Change Settings to enable editing.
3. Select VMNet8 and update the Subnet IP to `10.0.0.0`.
4. Open DHCP Settings and configure the appropriate address range for the VMs.
5. Click Apply and then OK to save the changes.

This setup ensures all virtual machines are on the same private network while retaining Internet connectivity through NAT.

---

### Downloading ISO Images

ISO files for the project can be obtained from the ISO provider. Note that downloading directly from the provider may result in version differences, which could slightly affect the UI but should not disrupt the project workflow.

---

### Creating a Virtual Machine

1. In VMware Workstation, select Create a New Virtual Machine.
2. Keep the default settings when prompted.
3. Browse and select the relevant `.iso` file for installation (refer to the Project Overview for file sources).
4. Assign a name to the virtual machine.
5. Set a TPM password if required, and ensure it is securely saved.
6. Leave the disk option as Split virtual disk into multiple files.

---

### Taking a Snapshot

To preserve the VM’s initial state:

- Right-click the virtual machine → Snapshot → Take Snapshot.

Snapshots allow easy rollback if changes cause issues during the project.

---

### Full-Screen Mode

VMware Workstation Pro supports native full-screen mode, providing an immersive experience for working with virtual machines.