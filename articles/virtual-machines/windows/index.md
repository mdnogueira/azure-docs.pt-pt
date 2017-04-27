---
title: "Documentação da Máquina Virtual do Azure para Windows - Tutoriais, Referência da API | Microsoft Docs"
description: "Saiba como criar máquinas virtuais do Windows. A documentação ajuda-o a planear, implementar e gerir um ambiente de computação de máquina virtual na cloud."
services: virtual-machines\windows
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\windows
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 631eb4c8aa3a41e03cf623fcc5610d3e84f18ec4
ms.lasthandoff: 04/22/2017

---
<div class="content">
    <h1>Documentação de Máquinas Virtuais do Windows</h1>
    <div class="introHolder" style="justify-content: space-between;">
        <div class="intro" style="min-width: 200px">
            <p>As Máquinas Virtuais do Windows no Azure fornecem uma infraestrutura a pedido, altamente dimensionável, segura e virtualizada através do Windows Server. Saiba como criar, configurar, gerir e dimensionar VMs do Windows com os nossos inícios rápidos, tutoriais e amostras.</p>
        </div>
        <a href="https://azure.microsoft.com/en-us/resources/videos/create-windows-server-virtual-machine/">
            <div class="calloutHolder" style="max-width: 250px">
                <div>
                    <img src="media/index/create-windows-server-virtual-machine.png" style="width: 250px" />
                </div>
                <div>
                    <p style="margin-top: 0; color: #6e6e6e">Crie uma Máquina Virtual do Windows Server. (3:58)</p>
                </div>
            </div>
        </a>
    </div>
<h2 style="margin-top: 0px; margin-bottom: 0px;">Inícios rápidos de cinco minutos</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">Saiba como implementar um servidor Web IIS numa Máquina Virtual que execute o Windows Server 2016:</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-windows-quick-create-portal?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>Portal do Azure</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-windows-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-windows-quick-create-cli?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>CLI do Azure</span>
        </a>
    </div>
</div>

<h2 style="margin-top: 36px">Tutoriais Passo-a-Passo</h2>
<p>Saiba como implementar, gerir e monitorizar VMs do Windows no Azure</p>
<ol>
   <li><a href="/azure/virtual-machines/windows/tutorial-manage-vm">Criar e gerir VMs do Windows</a></li>
    <li><a href="/azure/virtual-machines/windows/tutorial-manage-data-disk">Criar e gerir discos de VM</a></li>
    <li><a href="/azure/virtual-machines/windows/tutorial-automate-vm-deployment">Automatizar a configuração da VM</a></li>
    <li><a href="/azure/virtual-machines/windows/tutorial-load-balancer">Balanceamento de carga de VMs</a></li>
    <li><a href="/azure/virtual-machines/windows/tutorial-virtual-network">Gerir redes de VM</a></li>
</ol>

<h2 style="margin-top: 36px">Vídeo de Formação Gratuita da PluralSight</h2>
<ul class="panelContent cardsW">
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-infrastructure.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Gestão de infraestrutura</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-vms.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Introdução às Máquinas Virtuais</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-iaas-monitoring.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Introdução à Monitorização de IaaS</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<h2>Amostras</h2>
<p>Implemente a sua primeira aplicação no Azure.</p>
<ul class="spaced">
    <li><a href="/azure/virtual-machines/virtual-machines-windows-cli-samples?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json">CLI do Azure</a></li>
    <li><a href="/azure/virtual-machines/virtual-machines-windows-powershell-samples?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json">Azure PowerShell</a></li>
</ul>

<h2 style="margin-top: 36px">Referência</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Linha de Comandos</h3>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                        <p><a href="/cli/azure/vm">CLI do Azure</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Linguagens</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/develop/nodejs/#azure-sdk">Node.js</a></p>
                        <p><a href="http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.compute.html">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST</h3>
                        <p><a href="/rest/api/compute">Referência da API REST da Computação</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>

