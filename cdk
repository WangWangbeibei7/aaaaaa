import * as cdk from 'aws-cdk-lib';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import * as keypair from "cdk-ec2-key-pair"; 
import { Construct } from 'constructs';
import { CfnSubnet, CfnInternetGateway, CfnVPCGatewayAttachment, CfnRouteTable, CfnRoute, CfnSubnetRouteTableAssociation } from 'aws-cdk-lib/aws-ec2';
// import * as sqs from 'aws-cdk-lib/aws-sqs';

export class ShenaqiStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

     //Vpc
    const vpc = new ec2.Vpc(this, 'Vpcproduction', {
     cidr:"10.0.0.0/16",
     enableDnsHostnames: true,
     enableDnsSupport: true,
     // サブネットの自動作成はなし。
     subnetConfiguration: [], 
     vpcName: 'cpi-fang-test',  
   }); 
   
   
    //pub-1
    const publicSubnet1 =  new CfnSubnet(this, 'MyPublicSubnet1', {
     availabilityZone: 'ap-northeast-1a',
     cidrBlock: '10.0.1.0/24',
     vpcId: vpc.vpcId,
     mapPublicIpOnLaunch: false,
     tags: [{ key: "Name", value: "cpi-pub-1" }]
      }); 
      
    //pub-2
    const publicSubnet2 = new CfnSubnet(this, 'MyPublicSubnet2', {
     availabilityZone: 'ap-northeast-1c',
     cidrBlock: '10.0.2.0/24',
     vpcId: vpc.vpcId,
     mapPublicIpOnLaunch: false,
     tags: [{ key: "Name", value: "cpi-pub-2" }]
      }); 
      
    //pri-1
    const privateSubnet1 = new CfnSubnet(this, 'MyPrivateSubnet1', {
      availabilityZone: 'ap-northeast-1a',
      cidrBlock: '10.0.3.0/24',
      vpcId: vpc.vpcId,
      mapPublicIpOnLaunch: false,
      tags: [{ key: "Name", value: "cpi-pri-1" }]
      }); 
      
    //pri-2  
    const privateSubnet2 = new CfnSubnet(this, 'MyPrivateSubnet2', {
      availabilityZone: 'ap-northeast-1a',
      cidrBlock: '10.0.4.0/24',
      vpcId: vpc.vpcId,
      mapPublicIpOnLaunch: false,
      tags: [{ key: "Name", value: "cpi-pri-2" }]
      });
      
    //pri-3    
    const privateSubnet3 = new CfnSubnet(this, 'MyPrivateSubnet3', {
      availabilityZone: 'ap-northeast-1a',
      cidrBlock: '10.0.5.0/24',
      vpcId: vpc.vpcId,
      mapPublicIpOnLaunch: false,
      tags: [{ key: "Name", value: "cpi-pri-3" }]
      }); 
      
    //pri-4  
    const privateSubnet4 = new CfnSubnet(this, 'MyPrivateSubnet4', {
      availabilityZone: 'ap-northeast-1c',
      cidrBlock: '10.0.6.0/24',
      vpcId: vpc.vpcId,
      mapPublicIpOnLaunch: false,
      tags: [{ key: "Name", value: "cpi-pri-4" }]
      });  
      
    //igw  
    const igw = new CfnInternetGateway(this, 'igw', {
      tags: [{ key: "Name", value: "Igw" }]
    })
    new CfnVPCGatewayAttachment(this, 'igwAttachment', {
      internetGatewayId: igw.ref,
      vpcId: vpc.vpcId
    })
    
    //igw-rtb-pub1  igw-rtb-pub2
    const publicRouteTable = new CfnRouteTable(this, 'PublicRouteTable', {
        tags: [{ key: "Name", value: "rt-pub-01" }],
        vpcId: vpc.vpcId
      })
    new CfnRoute(this, 'PublicRoute', {
        routeTableId: publicRouteTable.ref,
        destinationCidrBlock: '0.0.0.0/0',
        gatewayId: igw.ref, 
      })
    new CfnSubnetRouteTableAssociation(this, 'PublicSubnet1RouteTableAssociation', {
        routeTableId: publicRouteTable.ref,
        subnetId: publicSubnet1.ref
      })
    new CfnSubnetRouteTableAssociation(this, 'PublicSubnet2RouteTableAssociation', {
        routeTableId: publicRouteTable.ref,
        subnetId: publicSubnet2.ref
      })
      
      
    //EIP二つ
    const eip1 = new ec2.CfnEIP(this, 'CfnEIP1', {
        tags: [{ key: 'Name', value: 'eip-01' }],
      });
    const eip2 = new ec2.CfnEIP(this, 'CfnEIP2', {
        tags: [{ key: 'Name', value: 'eip-02' }],
      }); 
      
      
  　//NAT１
    const ngw1 = new ec2.CfnNatGateway(this, 'CfnNatGateway1', {
        allocationId: eip1.attrAllocationId,
        subnetId: publicSubnet1.ref,
        tags: [{ key: 'Name', value: 'ngw-01' }],
      })
      
   //NAT２
    const ngw2 = new ec2.CfnNatGateway(this, 'CfnNatGateway2', {
        allocationId: eip2.attrAllocationId,
        subnetId: publicSubnet2.ref,
        tags: [{ key: 'Name', value: 'ngw-02' }],
      }) 
      
      
    //pri-rtb1-NAT１
    const privateRouteTable1 = new ec2.CfnRouteTable(this, 'priRouteTable1', {
        vpcId: vpc.vpcId,
        tags: [{ key: 'Name', value: 'rt-private-01' }],
      });
    new ec2.CfnRoute(this, 'priRoute1', {
        routeTableId: privateRouteTable1.ref,
        destinationCidrBlock: '0.0.0.0/0',
        natGatewayId: ngw1.ref,
      });
     
     
    //pri-rtb2-NAT２
    const privateRouteTable2 = new ec2.CfnRouteTable(this, 'priRouteTable2', {
        vpcId: vpc.vpcId,
        tags: [{ key: 'Name', value: 'rt-private-02' }],
      });
    new ec2.CfnRoute(this, 'priRoute2', {
        routeTableId: privateRouteTable2.ref,
        destinationCidrBlock: '0.0.0.0/0',
        natGatewayId: ngw2.ref,
      });
    new ec2.CfnSubnetRouteTableAssociation(this, 'priSubnetRouteTableAssociation1', {
        routeTableId: privateRouteTable1.ref,
        subnetId: privateSubnet1.ref ,
      });
     new ec2.CfnSubnetRouteTableAssociation(this, 'priSubnetRouteTableAssociation2', {
        routeTableId: privateRouteTable1.ref,
        subnetId: privateSubnet2.ref ,
      });
     new ec2.CfnSubnetRouteTableAssociation(this, 'priSubnetRouteTableAssociation3', {
        routeTableId: privateRouteTable1.ref,
        subnetId: privateSubnet3.ref,
      });  
    new ec2.CfnSubnetRouteTableAssociation(this, 'priSubnetRouteTableAssociation4', {
        routeTableId: privateRouteTable2.ref,
        subnetId: privateSubnet4.ref ,
    });
    
    //SG
    const sgBastion = new ec2.CfnSecurityGroup(this, 'SecurityGroup1', {
      groupDescription: 'for server',
      vpcId:vpc.vpcId,
      securityGroupIngress: [{
        ipProtocol: 'tcp',
        fromPort: 22, 
        toPort: 22, 
        cidrIp: '0.0.0.0/0',
        description: 'for server',
      },{
        ipProtocol: 'tcp',
        fromPort: 443, 
        toPort: 443, 
        cidrIp: '0.0.0.0/0',
        description: 'for server',  
      },{
        ipProtocol: 'tcp',
        fromPort: 80, 
        toPort: 80, 
        cidrIp: '0.0.0.0/0',
        description: 'for server'
      }],
      tags: [{ key: 'Name', value: 'sg-server' }],
    })
    
  const key = new keypair.KeyPair(this, "KeyPair", {
      name: "nakagaki",
      description: "Key Pair ",
    });
    key.grantReadOnPublicKey; 
    
    
  const ami = new ec2.AmazonLinuxImage({
      generation: ec2.AmazonLinuxGeneration.AMAZON_LINUX_2,
      cpuType: ec2.AmazonLinuxCpuType.X86_64,
    });
    
    
  //
  const ec2Instance1 = new ec2.CfnInstance(this, 'jumpserver', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: publicSubnet1.ref,
        associatePublicIpAddress: true,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'jumpserver' }],
    }); 
    
    
  const ec2Instance2 = new ec2.CfnInstance(this, 'deepsecurit', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: publicSubnet1.ref,
        associatePublicIpAddress: true,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'deepsecurit' }],
    }); 
    
    
  const ec2Instance3 = new ec2.CfnInstance(this, 'workserver', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet1.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'workserver' }],
    });
    
    
  const ec2Instance4 = new ec2.CfnInstance(this, 'batch-server', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet1.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'batch-server' }],
    });  
    
    
  const ec2Instance5 = new ec2.CfnInstance(this, 'mailserver', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet2.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'mailserver' }],
    });  
    
    
  const ec2Instance6 = new ec2.CfnInstance(this, 'sorryserver', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet2.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'sorryserver' }],
    });  
    
    
  const ec2Instance7 = new ec2.CfnInstance(this, 'mypage-AP1', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet2.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'mypage-AP1' }],
    }); 
    
    
  const ec2Instance8 = new ec2.CfnInstance(this, 'WCC/CC-AP1', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1a',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet2.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'WCC/CC-AP1' }],
    });  
    
    
  const ec2Instance9 = new ec2.CfnInstance(this, 'mypage-AP2', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1c',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet4.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'mypage-AP2' }],
    });  
    
    
  const ec2Instance10 = new ec2.CfnInstance(this, 'WCC/CC-AP2', {
      imageId: ami.getImage(this).imageId,
      instanceType: 't2.micro',
      availabilityZone: 'ap-northeast-1c',
      networkInterfaces: [{
        deviceIndex: '0',
        groupSet: [sgBastion.ref, vpc.vpcDefaultSecurityGroup],
        subnetId: privateSubnet4.ref,
        associatePublicIpAddress: false,
      }],
      keyName: key.keyPairName,
      tags: [{ key: 'Name', value: 'WCC/CC-AP2' }],
    });   
    
    
    //ロードバランサ用
    const sgElb = new ec2.CfnSecurityGroup(this, 'SecurityGroup2', {
      groupDescription: 'for load balancer',
      vpcId: vpc.vpcId ,
      securityGroupIngress: [{
        ipProtocol: 'tcp',
        fromPort: 80, 
        toPort: 80, 
        cidrIp: '0.0.0.0/0',
        description: 'for load balancer',
      }, {
        ipProtocol: 'tcp',
        fromPort: 443, 
        toPort: 443, 
        cidrIp: '0.0.0.0/0',
        description: 'for load balancer',
      }],
      tags: [{ key: 'Name', value: 'sg-alb' }],
    }) 
  }
}
