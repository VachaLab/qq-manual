# Installation

This section explains how to easily install qq on different clusters.

All installation scripts assume that you are using bash as your shell. If you use a different shell, follow [this section](install_general.md#installing-a-pre-built-version-for-other-shells) of the manual.

## Updating qq

To **reinstall** or **update** qq on a cluster, just run the installation command for the given cluster again.

> Updating qq is *usually* safe, even if you have running qq jobs on the cluster. Jobs that are already running will continue using the old version of qq. Loop jobs will automatically switch to the updated version in their next cycle.