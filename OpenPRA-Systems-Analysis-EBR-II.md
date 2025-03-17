# Systems Analysis Documentation for EBR-II Reactor

> **Source Code Reference:** This documentation is based on the OpenPRA Systems Analysis TypeScript schema implementation available at [GitHub: openpra-org/openpra-monorepo](https://github.com/openpra-org/openpra-monorepo/tree/rahul/tech-element-16march/packages/shared-types/src/openpra-mef/technical-elements/systems-analysis)

## Table of Contents
1. [Introduction](#introduction)
2. [Schema Overview](#schema-overview)
3. [Regulatory Requirements Coverage](#regulatory-requirements-coverage)
   1. [SY-C1: Process Documentation](#sy-c1-process-documentation)
      1. [(a) System Function and Operation](#a-system-function-and-operation)
      2. [(b) System Model Boundary](#b-system-model-boundary)
      3. [(c) System Schematic](#c-system-schematic)
      4. [(d) Equipment Operability](#d-equipment-operability)
      5. [(e) Operational History](#e-operational-history)
      6. [(f) Success Criteria](#f-success-criteria)
      7. [(g) Human Actions](#g-human-actions)
      8. [(h) Test and Maintenance](#h-test-and-maintenance)
      9. [(i) System Dependencies](#i-system-dependencies)
      10. [(j) Component Spatial Information](#j-component-spatial-information)
      11. [(k) Modeling Assumptions](#k-modeling-assumptions)
      12. [(l) Components and Failure Modes](#l-components-and-failure-modes)
      13. [(m) Modularization Process](#m-modularization-process)
      14. [(n) Logic Loop Resolution](#n-logic-loop-resolution)
      15. [(o) Evaluation Results](#o-evaluation-results)
      16. [(p) Sensitivity Studies](#p-sensitivity-studies)
      17. [(q) Information Sources](#q-information-sources)
      18. [(r) Basic Events Traceability](#r-basic-events-traceability)
      19. [(s) Nomenclature](#s-nomenclature)
      20. [(t) Digital I&C Systems](#t-digital-ic-systems)
      21. [(u) Passive Systems](#u-passive-systems)
   2. [SY-C2: Model Uncertainty Documentation](#sy-c2-model-uncertainty-documentation)
   3. [SY-C3: Pre-Operational Assumptions Documentation](#sy-c3-pre-operational-assumptions-documentation)
4. [Implementation Examples](#implementation-examples)
   1. [EBR-II Passive Safety Features](#ebr-ii-passive-safety-features)
   2. [EBR-II Shutdown Cooling System](#ebr-ii-shutdown-cooling-system)
   3. [EBR-II Primary Sodium System](#ebr-ii-primary-sodium-system)
   4. [EBR-II Reactor Shutdown System](#ebr-ii-reactor-shutdown-system)
5. [Requirements Coverage Summary](#requirements-coverage-summary)
6. [Conclusion](#conclusion)

## Introduction

This document demonstrates how the TypeScript schema for Systems Analysis is uniquely suited for comprehensively documenting the Experimental Breeder Reactor-II (EBR-II). The schema provides a structured framework aligned with regulatory requirements that enables detailed capture of EBR-II's distinctive design features, particularly its passive safety characteristics.

The EBR-II was a sodium-cooled fast breeder reactor operated at Idaho National Laboratory from 1964 to 1994. It served as both a power plant and a test facility, providing valuable data on liquid metal fast breeder reactor (LMFBR) technology. Its unique design incorporated inherent safety features that were demonstrated through landmark tests including the loss-of-flow and loss-of-heat-sink tests performed in 1986.

The primary objective of this document is to demonstrate how the TypeScript schema enables thorough, traceable documentation of EBR-II's systems, supporting Probabilistic Risk Assessment (PRA) and regulatory compliance. The schema's structure aligns perfectly with the needs of documenting a complex reactor design characterized by passive safety features, natural circulation cooling capabilities, and unique sodium coolant properties.

## Schema Overview

The Systems Analysis TypeScript schema implements a comprehensive set of interfaces and types that enable detailed documentation of EBR-II's system models, dependencies, uncertainties, and assumptions. Key modules within the schema particularly relevant to EBR-II include:

```typescript
// Core interfaces for system definition
export interface SystemDefinition extends Unique, Named {
  description?: string;
  boundaries: string[];
  successCriteria: string | SystemSuccessCriterion | SuccessCriteriaId;
  missionTime?: string;
  schematic?: {
    reference: string;
    description?: string;
  };
  operabilityConsiderations?: {
    component: string;
    calculationRef?: string;
    notes?: string;
  }[];
  operationalHistory?: string[];
  humanActionsForOperation?: {
    actionRef: HumanActionReference;
    description: string;
  }[];
  testAndMaintenance?: string[];
  modelAssumptions?: string[];
  spatialInformation?: {
    location: string;
    hazards?: string[];
    components?: string[];
  }[];
  modeledComponentsAndFailures: Record<
    string,
    {
      failureModes: string[];
      justificationForInclusion?: string;
      componentGroup?: string;
    }
  >;
  justificationForExclusionOfComponents?: string[];
  justificationForExclusionOfFailureModes?: string[];
}

// Passive systems treatment interface, crucial for EBR-II documentation
export interface PassiveSystemsTreatment extends Unique, Named {
  systemReference: SystemReference;
  description: string;
  performanceAnalysisRef?: string;
  uncertaintyAnalysis?: string;
  relevantPhysicalPhenomena?: string[];
  uncertaintyEvaluation?: string;
}
```

The schema includes specialized interfaces for documenting EBR-II's key characteristics:

* **System modeling and failure modes**: Enables structured representation of EBR-II's front-line and support systems, including the primary sodium system, shutdown cooling system, and reactor shutdown system.

* **Passive safety features**: Dedicated interfaces for documenting EBR-II's inherent safety characteristics, including negative reactivity feedback, natural circulation cooling, and large thermal capacity.

* **Dependencies and common cause analysis**: Supports the documentation of dependencies between EBR-II systems, a crucial aspect given the interconnected nature of sodium coolant systems.

* **Engineering analysis validation**: Provides structures for linking system models to supporting analyses such as those performed using the SASSYS code for thermal-hydraulic simulations.

## Regulatory Requirements Coverage

This section demonstrates how the schema enables comprehensive compliance with each supporting requirement for regulatory compliance in the context of the EBR-II reactor.

### SY-C1: Process Documentation

The schema provides the `ProcessDocumentation` interface that extends `BaseProcessDocumentation` with systems-specific documentation requirements, ideally suited for documenting EBR-II's unique systems:

```typescript
export interface ProcessDocumentation extends BaseProcessDocumentation {
  systemFunctionDocumentation?: Record<SystemReference, string>;
  systemBoundaryDocumentation?: Record<SystemReference, string>;
  // Additional properties addressing all SY-C1 requirements (a) through (u)
  // ...
}
```

#### (a) System Function and Operation

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
systemFunctionDocumentation?: Record<SystemReference, string>;

// Also in SystemDefinition interface
description?: string;
```

**EBR-II Example (Primary Sodium System):**
```typescript
const processDoc: ProcessDocumentation = {
  systemFunctionDocumentation: {
    "SYS-PrimarySodium": "The primary sodium system circulates liquid sodium coolant through the reactor core to remove heat generated by fission. The system consists of two primary pumps, the reactor vessel, and associated piping. During normal operation, the sodium transfers heat to the secondary coolant system via the intermediate heat exchanger (IHX). Upon loss of forced circulation, the system is designed to establish natural circulation flow paths that provide passive decay heat removal capability, a key inherent safety feature of EBR-II."
  }
};

const primarySodiumSystem: SystemDefinition = {
  id: "SYS-PrimarySodium",
  name: "Primary Sodium System",
  description: "System for circulating liquid sodium coolant through the EBR-II core, characterized by its capability to transition to natural circulation cooling during loss of forced flow events. The system operates at near-atmospheric pressure with sodium inlet temperature of approximately 371°C and outlet temperature of approximately 473°C during full power operation."
};
```

#### (b) System Model Boundary

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
systemBoundaryDocumentation?: Record<SystemReference, string>;

// Also in SystemDefinition interface
boundaries: string[];
```

**EBR-II Example (Shutdown Cooling System):**
```typescript
const systemDef: SystemDefinition = {
  id: "SYS-ShutdownCooling",
  name: "Shutdown Cooling System",
  boundaries: [
    "Bayonet heat exchangers immersed in the primary sodium pool",
    "NaK-filled natural circulation loops",
    "Air-cooled heat exchangers located in the stack exhaust system",
    "Includes stack exhaust system dampers and natural draft chimney",
    "Excludes primary sodium system boundary",
    "Interface with the reactor building stack"
  ]
};

const processDoc: ProcessDocumentation = {
  systemBoundaryDocumentation: {
    "SYS-ShutdownCooling": "The Shutdown Cooling System boundary includes the bayonet-type heat exchangers immersed in the primary tank sodium, the NaK-filled intermediate loops operating by natural circulation, and the air-cooled heat exchangers located in the reactor building stack. The system interfaces with the primary sodium pool but does not include the primary sodium system itself. The stack and associated dampers that provide the natural draft air flow are considered part of the system boundary."
  }
};
```

#### (c) System Schematic

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
systemSchematicReferences?: Record<SystemReference, {
  reference: string;
  description: string;
}>;

// Also in SystemDefinition interface
schematic?: {
  reference: string;
  description?: string;
};
```

**EBR-II Example (Primary Cooling System):**
```typescript
const processDoc: ProcessDocumentation = {
  systemSchematicReferences: {
    "SYS-PrimarySodium": {
      reference: "Drawing EBR-II-PCS-001, 'EBR-II Primary System Flow Diagram'",
      description: "Detailed flow diagram showing the primary sodium system components, including the reactor vessel, primary pumps, IHX, and flow paths."
    },
    "SYS-ShutdownCooling": {
      reference: "Drawing EBR-II-SCS-001, 'EBR-II Shutdown Cooling System Schematic'",
      description: "Schematic showing the NaK-filled natural circulation loops, bayonet heat exchangers, and air-cooled heat exchangers."
    }
  }
};

const primarySystem: SystemDefinition = {
  // Other properties...
  schematic: {
    reference: "ANL Drawing EBR-II-PCS-001",
    description: "Primary system arrangement showing Z-pipe configuration that facilitates natural circulation flow paths upon loss of forced flow."
  }
};
```

#### (d) Equipment Operability

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
equipmentOperabilityDocumentation?: Record<string, {
  system: SystemReference;
  component: string;
  considerations: string;
  calculationReferences?: string[];
}>;

// Also in SystemDefinition interface
operabilityConsiderations?: {
  component: string;
  calculationRef?: string;
  notes?: string;
}[];
```

**EBR-II Example (Primary Sodium System):**
```typescript
const processDoc: ProcessDocumentation = {
  equipmentOperabilityDocumentation: {
    "PrimaryPump-P1": {
      system: "SYS-PrimarySodium",
      component: "Primary Sodium Pump P-1",
      considerations: "The primary pump is designed to operate with sodium temperatures ranging from 371°C to 482°C. Pump coastdown characteristics ensure a gradual transition to natural circulation upon pump trip.",
      calculationReferences: ["CALC-EBR-PCS-002: 'Primary Pump Coastdown Analysis'"]
    },
    "IHX-01": {
      system: "SYS-PrimarySodium",
      component: "Intermediate Heat Exchanger",
      considerations: "The IHX is designed to operate with a primary side inlet temperature of approximately 473°C and a secondary side inlet temperature of approximately 320°C.",
      calculationReferences: ["CALC-EBR-PCS-003: 'IHX Thermal Performance Analysis'"]
    }
  }
};

const primarySystem: SystemDefinition = {
  // Other properties...
  operabilityConsiderations: [
    {
      component: "Primary Sodium Pump P-1",
      calculationRef: "CALC-EBR-PCS-002",
      notes: "Pump coastdown characteristics provide sufficient initial momentum to establish natural circulation flow path."
    },
    {
      component: "Z-Pipe Configuration",
      calculationRef: "CALC-EBR-PCS-004",
      notes: "Z-pipe design ensures adequate natural circulation flow even with complete loss of forced flow."
    }
  ]
};
```

#### (e) Operational History

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
operationalHistoryDocumentation?: Record<SystemReference, string[]>;

// Also in SystemDefinition interface
operationalHistory?: string[];
```

**EBR-II Example (Passive Safety Demonstration):**
```typescript
const processDoc: ProcessDocumentation = {
  operationalHistoryDocumentation: {
    "SYS-PrimarySodium": [
      "In April 1986, EBR-II conducted a landmark test demonstrating inherent safety characteristics. The loss-of-flow test was performed by shutting off power to both primary pumps without scramming the reactor. The natural reactivity feedbacks and transition to natural circulation successfully prevented any damage to the core.",
      "A loss-of-heat-sink test was also conducted in 1986, demonstrating the ability of the system to safely respond to complete loss of heat removal capability through the balance of plant.",
      "Throughout its operational history (1964-1994), the primary sodium system demonstrated highly reliable performance, with no significant sodium leaks from the primary tank."
    ]
  }
};

const rssSystem: SystemDefinition = {
  // Other properties...
  operationalHistory: [
    "The Reactor Shutdown System functioned as designed during the 30 years of EBR-II operation.",
    "Two spurious scrams occurred in 1985 due to instrumentation drift in the high neutron flux trip channel.",
    "The system was not required to actuate during the 1986 inherent safety demonstration tests, demonstrating the effectiveness of passive safety features."
  ]
};
```

#### (f) Success Criteria

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
successCriteriaDocumentation?: Record<SystemReference, {
  criteria: string;
  relationshipToEventSequences: string;
}>;

// Also in SystemDefinition interface
successCriteria: string | SystemSuccessCriterion | SuccessCriteriaId;

// Also in SupportSystemSuccessCriteria interface
export interface SupportSystemSuccessCriteria extends Unique {
  systemReference: SystemReference;
  successCriteria: string;
  criteriaType: "conservative" | "realistic";
  supportedSystems: SystemReference[];
}
```

**EBR-II Example (Primary Sodium System):**
```typescript
const processDoc: ProcessDocumentation = {
  successCriteriaDocumentation: {
    "SYS-PrimarySodium": {
      criteria: "Establish and maintain natural circulation flow sufficient to maintain peak cladding temperatures below 650°C during loss of forced flow events.",
      relationshipToEventSequences: "Critical for all sequences involving loss of forced circulation, including station blackout scenarios and primary pump failures. Natural circulation capability effectively mitigates unprotected loss of flow (ULOF) sequences that would otherwise lead to core damage in reactors without this passive feature."
    },
    "SYS-ShutdownCooling": {
      criteria: "Remove sufficient decay heat to maintain primary sodium temperature below 550°C following reactor shutdown.",
      relationshipToEventSequences: "Required for extended cooling following reactor shutdown when the main heat removal path through the secondary sodium system is unavailable."
    }
  }
};

// Example for support system success criteria
const supportSystemCriteria: SupportSystemSuccessCriteria = {
  id: "SSSC-Stack-001",
  systemReference: "SYS-StackExhaust",
  successCriteria: "Provide sufficient natural draft airflow to maintain air-cooled heat exchanger performance in the shutdown cooling system.",
  criteriaType: "realistic",
  supportedSystems: ["SYS-ShutdownCooling"]
};
```

#### (g) Human Actions

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
humanActionsDocumentation?: Record<HumanActionReference, {
  system: SystemReference;
  description: string;
}>;

// Also in SystemDefinition interface
humanActionsForOperation?: {
  actionRef: HumanActionReference;
  description: string;
}[];
```

**EBR-II Example (Shutdown Cooling System):**
```typescript
const processDoc: ProcessDocumentation = {
  humanActionsDocumentation: {
    "HRA-SCS-001": {
      system: "SYS-ShutdownCooling",
      description: "Operator verification of air damper positions to ensure proper cooling airflow through shutdown coolers following automatic system actuation."
    },
    "HRA-RSS-001": {
      system: "SYS-RSS",
      description: "Manual reactor scram upon recognition of off-normal conditions not automatically detected by the protection system."
    }
  }
};

const shutdownCoolingSystem: SystemDefinition = {
  // Other properties...
  humanActionsForOperation: [
    {
      actionRef: "HRA-SCS-001",
      description: "Operator verification of air damper positions within 2 hours of system actuation."
    },
    {
      actionRef: "HRA-SCS-002",
      description: "Periodic monitoring of NaK loop temperatures to verify natural circulation is established."
    }
  ]
};
```

#### (h) Test and Maintenance

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
testMaintenanceProceduresDocumentation?: Record<SystemReference, string[]>;

// Also in SystemDefinition interface
testMaintenanceProcedures?: string[];
testAndMaintenance?: string[];
```

**EBR-II Example (Reactor Shutdown System):**
```typescript
const processDoc: ProcessDocumentation = {
  testMaintenanceProceduresDocumentation: {
    "SYS-RSS": [
      "Procedure RSS-MT-001: Monthly testing of safety rod drive mechanisms",
      "Procedure RSS-MT-002: Quarterly calibration of neutron flux instrumentation",
      "Procedure RSS-MT-003: Annual inspection of safety rod dashpots"
    ],
    "SYS-ShutdownCooling": [
      "Procedure SCS-MT-001: Quarterly verification of air damper operation",
      "Procedure SCS-MT-002: Annual inspection of NaK expansion tanks and level indicators"
    ]
  }
};

const rssSystem: SystemDefinition = {
  // Other properties...
  testAndMaintenance: [
    "Monthly testing of safety rod insertion times",
    "Quarterly calibration of neutron flux instrumentation",
    "Semi-annual testing of manual scram circuits",
    "Annual inspection of safety rod drive mechanisms during refueling outages"
  ]
};
```

#### (i) System Dependencies

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
dependencySearchDocumentation?: {
  methodology: string;
  dependencyTableReferences: string[];
};

// Also in dedicated interfaces
export interface SystemDependency extends Unique {
  dependentSystem: SystemReference;
  supportingSystem: SystemReference;
  type: DependencyType | string;
  // Other properties...
}

export interface DependencySearchMethodology extends Unique, Named {
  description: string;
  reference: string;
  dependencyTables?: {
    tableId: string;
    description: string;
    reference?: string;
  }[];
  // Other properties...
}
```

**EBR-II Example (System Dependencies):**
```typescript
const dependencySearch: DependencySearchMethodology = {
  id: "DSM-EBR-001",
  name: "EBR-II Systems Dependency Analysis",
  description: "Systematic approach to identify functional, spatial, and human dependencies between EBR-II systems based on documentation review, system walkdowns, and interviews with cognizant engineers.",
  reference: "Report EBR-II-SDA-001: 'EBR-II Systems Dependency Analysis'",
  dependencyTables: [
    {
      tableId: "DEP-PrimarySodium-001",
      description: "Primary Sodium System Dependencies"
    },
    {
      tableId: "DEP-ShutdownCooling-001",
      description: "Shutdown Cooling System Dependencies"
    }
  ]
};

const systemDependencies: SystemDependency[] = [
  {
    id: "DEP-001",
    dependentSystem: "SYS-PrimarySodium",
    supportingSystem: "SYS-ElectricalPower",
    type: "Functional",
    description: "Primary pumps require electrical power for forced circulation, though natural circulation capability exists upon loss of power."
  },
  {
    id: "DEP-002",
    dependentSystem: "SYS-ShutdownCooling",
    supportingSystem: "SYS-StackExhaust",
    type: "Functional",
    description: "Stack exhaust system provides natural draft for air cooling of shutdown coolers."
  },
  {
    id: "DEP-003",
    dependentSystem: "SYS-RSS",
    supportingSystem: "SYS-ElectricalPower",
    type: "Functional",
    description: "Scram breakers require DC power to remain energized; loss of power results in automatic scram (fail-safe design)."
  }
];

const processDoc: ProcessDocumentation = {
  dependencySearchDocumentation: {
    methodology: "Dependencies were identified through systematic review of EBR-II system design documents, interviews with operations personnel, and system walkdowns. Special attention was given to identifying dependencies that could affect passive safety features.",
    dependencyTableReferences: [
      "Table 3-1: 'EBR-II System Dependency Matrix' in EBR-II PRA Final Report"
    ]
  }
};
```

#### (j) Component Spatial Information

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
spatialInformationDocumentation?: Record<string, {
  location: string;
  systems: SystemReference[];
  components: string[];
  hazards: string[];
}>;

// Also in SystemDefinition interface
spatialInformation?: {
  location: string;
  hazards?: string[];
  components?: string[];
}[];
```

**EBR-II Example (Primary Tank and Reactor Building):**
```typescript
const processDoc: ProcessDocumentation = {
  spatialInformationDocumentation: {
    "PrimaryTank": {
      location: "EBR-II Primary Tank",
      systems: ["SYS-PrimarySodium", "SYS-RSS", "SYS-FuelHandling"],
      components: ["Reactor Core", "Primary Pumps", "IHX", "High-Pressure Plenum", "Low-Pressure Plenum", "Z-Pipe"],
      hazards: ["High radiation", "High temperature sodium", "Potential sodium fires if containment breached"]
    },
    "ReactorBuilding": {
      location: "EBR-II Reactor Building",
      systems: ["SYS-ShutdownCooling", "SYS-ElectricalPower", "SYS-StackExhaust"],
      components: ["Shutdown Coolers", "Electrical Distribution Panels", "Stack and Dampers"],
      hazards: ["Limited access during operation due to radiation", "Potential sodium fire smoke in case of leaks"]
    }
  }
};

const primarySystem: SystemDefinition = {
  // Other properties...
  spatialInformation: [
    {
      location: "Primary Tank",
      hazards: ["High temperature sodium", "High radiation levels"],
      components: ["Reactor core", "Primary pumps P-1 and P-2", "Intermediate Heat Exchanger", "High-pressure plenum", "Low-pressure plenum", "Z-pipe"]
    },
    {
      location: "Primary Pump Cells",
      hazards: ["Radiation", "Potential sodium leakage"],
      components: ["Primary pump motors", "Primary pump instrumentation"]
    }
  ]
};
```

#### (k) Modeling Assumptions

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
modelingAssumptionsDocumentation?: Record<SystemReference, string[]>;

// Also in SystemDefinition interface
modelAssumptions?: string[];
```

**EBR-II Example (Passive Safety Features):**
```typescript
const processDoc: ProcessDocumentation = {
  modelingAssumptionsDocumentation: {
    "SYS-PrimarySodium": [
      "Natural circulation flow is established within 60 seconds following loss of forced flow, based on experimental data from the 1986 loss-of-flow tests.",
      "The negative reactivity feedback coefficients are sufficient to reduce reactor power to decay heat levels within 5 minutes of an unprotected loss of flow event, without exceeding safety limits.",
      "The Z-pipe configuration is sufficient to establish natural circulation flow without credit for any active components.",
      "The large sodium inventory provides sufficient thermal inertia to prevent rapid temperature increases during transients."
    ],
    "SYS-Core": [
      "Negative temperature and power coefficients of reactivity are constant across the operating range and are based on measured values from EBR-II operation.",
      "The metallic fuel's high thermal conductivity allows for rapid heat transfer to the sodium coolant, preventing localized overheating.",
      "Fuel expansion provides prompt negative reactivity feedback during power transients."
    ]
  }
};

const shutdownCoolingSystem: SystemDefinition = {
  // Other properties...
  modelAssumptions: [
    "NaK natural circulation establishes automatically upon sensing elevated primary sodium temperatures.",
    "Air flow through shutdown coolers is driven by natural draft through the stack without requiring forced ventilation.",
    "Heat removal capacity is sufficient for decay heat levels 1 hour after shutdown.",
    "Air dampers fail in the open position, ensuring cooling capability even with loss of power."
  ]
};
```

#### (l) Components and Failure Modes

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
componentsFailureModesDocumentation?: Record<SystemReference, {
  includedComponents: string[];
  excludedComponents: string[];
  justificationForExclusion: string;
  includedFailureModes: string[];
  excludedFailureModes: string[];
  justificationForFailureModeExclusion: string;
}>;

// Also in SystemDefinition interface
modeledComponentsAndFailures: Record<
  string,
  {
    failureModes: string[];
    justificationForInclusion?: string;
    componentGroup?: string;
  }
>;
justificationForExclusionOfComponents?: string[];
justificationForExclusionOfFailureModes?: string[];
```

**EBR-II Example (Shutdown Cooling System):**
```typescript
const processDoc: ProcessDocumentation = {
  componentsFailureModesDocumentation: {
    "SYS-ShutdownCooling": {
      includedComponents: [
        "Bayonet heat exchangers", 
        "NaK loops", 
        "Air-cooled heat exchangers",
        "Air dampers",
        "NaK expansion tanks"
      ],
      excludedComponents: [
        "Small-bore instrument lines",
        "Local temperature indicators"
      ],
      justificationForExclusion: "Small-bore lines and local indicators do not significantly impact system function and their failure would be detected during routine monitoring.",
      includedFailureModes: [
        "Heat exchanger failure to transfer heat",
        "NaK loop blockage",
        "NaK leakage",
        "Air damper failure to open",
        "Air flow blockage"
      ],
      excludedFailureModes: [
        "Partial degradation of heat transfer"
      ],
      justificationForFailureModeExclusion: "Partial degradation would not prevent successful fulfillment of system function given the conservative design margins."
    }
  }
};

const shutdownCoolingSystem: SystemDefinition = {
  // Other properties...
  modeledComponentsAndFailures: {
    "Bayonet-HX-01": {
      failureModes: ["FAILURE_TO_TRANSFER_HEAT", "NAK_LEAKAGE"],
      justificationForInclusion: "Critical component for heat transfer from primary sodium to NaK loop",
      componentGroup: "HeatExchangers"
    },
    "Air-HX-01": {
      failureModes: ["FAILURE_TO_TRANSFER_HEAT", "NAK_LEAKAGE"],
      justificationForInclusion: "Critical component for heat rejection to atmosphere",
      componentGroup: "HeatExchangers"
    },
    "Air-Damper-01": {
      failureModes: ["FAILURE_TO_OPEN", "FAILURE_TO_REMAIN_OPEN"],
      justificationForInclusion: "Required for establishing natural draft air flow",
      componentGroup: "Dampers"
    }
  },
  justificationForExclusionOfComponents: [
    "Instrumentation lines and sensors excluded due to minimal impact on system function",
    "Local indicators excluded as their failure would not affect system operation"
  ],
  justificationForExclusionOfFailureModes: [
    "Partial heat transfer degradation excluded due to conservative design margins",
    "Short-term air flow fluctuations excluded due to thermal inertia of the system"
  ]
};
```

#### (m) Modularization Process

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
modularizationDocumentation?: {
  description: string;
  systems: SystemReference[];
};
```

**EBR-II Example (PRA Modularization):**
```typescript
const processDoc: ProcessDocumentation = {
  modularizationDocumentation: {
    description: "The EBR-II PRA employed a modular approach to system modeling. Front-line systems were modeled using fault trees linked to event trees representing accident sequences. Support system dependencies were incorporated directly into the appropriate front-line system fault trees rather than developing separate fault trees for each support system. This approach was particularly appropriate for EBR-II given the relatively simple support system requirements for passive safety features.",
    systems: [
      "SYS-RSS",
      "SYS-PrimarySodium",
      "SYS-ShutdownCooling",
      "SYS-SecondarySystem"
    ]
  }
};
```

#### (n) Logic Loop Resolution

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
logicLoopResolutionsDocumentation?: Record<string, {
  system: SystemReference;
  loopDescription: string;
  resolution: string;
}>;

// Also in SystemLogicModel interface
logicLoopResolutions?: {
  loopId: string;
  resolution: string;
}[];
```

**EBR-II Example (Electrical System Dependencies):**
```typescript
const processDoc: ProcessDocumentation = {
  logicLoopResolutionsDocumentation: {
    "Loop-EBR-Electrical-001": {
      system: "SYS-ElectricalPower",
      loopDescription: "Potential circular logic in modeling electrical power dependencies for cooling systems that protect electrical distribution equipment.",
      resolution: "Resolution implemented by explicitly modeling the timeline of dependencies. Electrical power is required for initial cooling system operation, but passive cooling mechanisms can maintain equipment within temperature limits following loss of power. The sequence of failures was modeled with proper time dependencies to break the logical loop."
    }
  }
};

const sysLogicModel: SystemLogicModel = {
  id: "SLM-ElectricalPower-001",
  systemReference: "SYS-ElectricalPower",
  description: "Logic model for EBR-II electrical power distribution system",
  modelRepresentation: "See Appendix B of EBR-II PRA Final Report",
  basicEvents: [],
  logicLoopResolutions: [
    {
      loopId: "Loop-EBR-Electrical-001",
      resolution: "Timeline-based modeling approach used to resolve circular dependencies between electrical systems and cooling systems."
    },
    {
      loopId: "Loop-EBR-Electrical-002",
      resolution: "Natural circulation capability in primary system modeled as independent of electrical power after initial establishment, breaking potential logic loops."
    }
  ]
};
```

#### (o) Evaluation Results

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
evaluationResultsDocumentation?: Record<SystemReference, {
  topEventProbability: number;
  otherResults: Record<string, string>;
}>;

// Also in SystemModelEvaluation interface
export interface SystemModelEvaluation extends Unique {
  system: SystemReference;
  topEventProbability?: number;
  quantitativeResults?: Record<string, number>;
  qualitativeInsights?: string[];
  dominantContributors?: {
    contributor: string;
    contribution: number;
  }[];
}
```

**EBR-II Example (System Failure Probabilities):**
```typescript
const processDoc: ProcessDocumentation = {
  evaluationResultsDocumentation: {
    "SYS-RSS": {
      topEventProbability: 1.2e-5,
      otherResults: {
        "Dominant Failure Mode": "Common cause failure of safety rod insertion mechanisms",
        "Risk Significance": "Low due to passive safety features that mitigate consequences of ATWS events"
      }
    },
    "SYS-ShutdownCooling": {
      topEventProbability: 3.7e-4,
      otherResults: {
        "Dominant Failure Mode": "Common cause failure of air dampers to open",
        "Risk Significance": "Moderate, but mitigated by multiple heat removal paths and large thermal inertia"
      }
    }
  }
};

const sysEvaluation: SystemModelEvaluation = {
  id: "EVAL-PrimarySodium-001",
  system: "SYS-PrimarySodium",
  topEventProbability: 2.5e-6, // Probability of failure to establish natural circulation
  quantitativeResults: {
    "Failure to establish natural circulation": 2.5e-6,
    "Insufficient natural circulation flow": 3.8e-5
  },
  qualitativeInsights: [
    "Natural circulation capability is highly reliable due to passive driving forces",
    "The Z-pipe configuration ensures flow path availability even with localized blockages",
    "Large thermal inertia provides significant margin for establishing natural circulation"
  ],
  dominantContributors: [
    {
      contributor: "Flow blockage in multiple assemblies",
      contribution: 0.45
    },
    {
      contributor: "Failure of reactor vessel internal structures",
      contribution: 0.30
    }
  ]
};
```

#### (p) Sensitivity Studies

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
sensitivityStudiesDocumentation?: Record<SystemReference, {
  studyDescription: string;
  results: string;
}[]>;

// Also in dedicated interface
export interface SystemSensitivityStudy extends SensitivityStudy {
  system: SystemReference;
  parameterChanged: string;
  impactOnSystem: string;
  insights?: string;
}
```

**EBR-II Example (Reactivity Feedback Sensitivity):**
```typescript
const processDoc: ProcessDocumentation = {
  sensitivityStudiesDocumentation: {
    "SYS-Core": [
      {
        studyDescription: "Variation of negative reactivity feedback coefficients",
        results: "Study showed that EBR-II's inherent shutdown capability remains effective even with reactivity coefficients reduced to 50% of their nominal values, demonstrating substantial safety margin."
      },
      {
        studyDescription: "Impact of uncertainties in natural circulation flow rates",
        results: "Variations in natural circulation flow rates from 50% to 150% of nominal values resulted in peak cladding temperature variations of +/-75°C, remaining well below safety limits."
      }
    ],
    "SYS-ShutdownCooling": [
      {
        studyDescription: "Variation in shutdown cooler heat transfer coefficients",
        results: "Reduction in heat transfer coefficients by 30% resulted in temperature increases of approximately 50°C, still maintaining sufficient margin to safety limits."
      }
    ]
  }
};

const sensitivityStudy: SystemSensitivityStudy = {
  id: "SENS-ReactivityFeedback-001",
  name: "Impact of Reactivity Feedback Variations on ATWS Response",
  system: "SYS-Core",
  parameterChanged: "Negative temperature coefficient of reactivity",
  impactOnSystem: "Variation of negative temperature coefficient from 50% to 150% of nominal value resulted in peak temperatures during ULOF events ranging from 650°C to 520°C, all below the 700°C limit.",
  insights: "Even with substantial degradation in reactivity feedback, EBR-II's inherent safety characteristics prevent core damage during unprotected transients, demonstrating robust passive safety."
};
```

#### (q) Information Sources

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
informationSourcesDocumentation?: {
  drawings: string[];
  procedures: string[];
  interviews: string[];
  otherSources: string[];
};
```

**EBR-II Example:**
```typescript
const processDoc: ProcessDocumentation = {
  informationSourcesDocumentation: {
    drawings: [
      "EBR-II System Design Descriptions (SDDs)",
      "Piping and Instrumentation Diagrams (P&IDs)",
      "EBR-II Primary System Arrangement Drawing No. 12D-12345",
      "EBR-II Shutdown Cooler Assembly Drawing No. 13A-56789"
    ],
    procedures: [
      "EBR-II Operating Instructions (OIs)",
      "EBR-II System Operating Procedures (SOPs)",
      "EBR-II Maintenance Procedures",
      "EBR-II Surveillance Test Procedures"
    ],
    interviews: [
      "Interview with EBR-II Operations Manager (1992)",
      "Interview with EBR-II Systems Engineer responsible for primary system (1992)",
      "Interview with EBR-II Instrumentation Engineer (1992)"
    ],
    otherSources: [
      "ANL-7323: 'Hazard Summary Report for EBR-II'",
      "ANL-RDP-105: 'Results of LOFWS/LOHSWS Safety Tests'",
      "ANL/CP-74542: 'EBR-II Inherent Safety Demonstration Tests'",
      "NUREG/CR-6042: 'PRA Procedures Guide'",
      "Generic nuclear industry data for component reliability"
    ]
  }
};
```

#### (r) Basic Events Traceability

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
basicEventsDocumentation?: Record<string, {
  system: SystemReference;
  description: string;
  moduleReference?: string;
  cutsetReference?: string;
}>;

// Also in FaultTree interface
export interface FaultTree extends Unique, Named {
  systemReference: SystemReference;
  nodes: Record<string, FaultTreeNode>;
  minimalCutSets?: string[][];
  // Other properties...
}
```

**EBR-II Example (Shutdown Cooling System):**
```typescript
const processDoc: ProcessDocumentation = {
  basicEventsDocumentation: {
    "BE-SCS-DAMPER-FTO": {
      system: "SYS-ShutdownCooling",
      description: "Air damper fails to open on demand",
      moduleReference: "MOD-SCS-DAMPER",
      cutsetReference: "CS-SCS-001"
    },
    "BE-SCS-HX-FTF": {
      system: "SYS-ShutdownCooling",
      description: "Shutdown cooler heat exchanger fails to transfer heat",
      moduleReference: "MOD-SCS-HX",
      cutsetReference: "CS-SCS-002"
    },
    "BE-SCS-NATCIRC-FTE": {
      system: "SYS-ShutdownCooling",
      description: "Natural circulation in NaK loop fails to establish",
      moduleReference: "MOD-SCS-NATCIRC",
      cutsetReference: "CS-SCS-003"
    }
  }
};

const faultTree: FaultTree = {
  id: "FT-SCS-001",
  name: "Shutdown Cooling System Fault Tree",
  systemReference: "SYS-ShutdownCooling",
  nodes: {
    "TOP": {
      id: "TOP",
      type: "OR",
      description: "Shutdown Cooling System Fails to Remove Decay Heat",
      children: ["G-SCS-01", "G-SCS-02", "G-SCS-03"]
    },
    "G-SCS-01": {
      id: "G-SCS-01",
      type: "AND",
      description: "Failure of Primary NaK Heat Exchangers",
      children: ["BE-SCS-HX1-FTF", "BE-SCS-HX2-FTF"]
    },
    // Additional nodes would be defined here
  },
  minimalCutSets: [
    ["BE-SCS-HX1-FTF", "BE-SCS-HX2-FTF"],
    ["BE-SCS-NATCIRC-FTE"],
    ["BE-SCS-DAMPER1-FTO", "BE-SCS-DAMPER2-FTO"]
  ]
};
```

#### (s) Nomenclature

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
nomenclatureDocumentation?: Record<string, string>;

// Also in SystemLogicModel interface
nomenclature?: Record<string, string>;
```

**EBR-II Example:**
```typescript
const processDoc: ProcessDocumentation = {
  nomenclatureDocumentation: {
    "ATWS": "Anticipated Transient Without Scram",
    "IHX": "Intermediate Heat Exchanger",
    "LOF": "Loss of Flow",
    "LOHSWS": "Loss of Heat Sink Without Scram",
    "LOFWS": "Loss of Flow Without Scram",
    "NaK": "Sodium-Potassium Eutectic Coolant",
    "RSS": "Reactor Shutdown System",
    "SCS": "Shutdown Cooling System",
    "ULOF": "Unprotected Loss of Flow"
  }
};

const sysLogicModel: SystemLogicModel = {
  // Other properties...
  nomenclature: {
    "FTO": "Fails to Open",
    "FTC": "Fails to Close",
    "FTR": "Fails to Run",
    "FTS": "Fails to Start",
    "FTF": "Fails to Function",
    "NATCIRC": "Natural Circulation",
    "HX": "Heat Exchanger"
  }
};
```

#### (t) Digital I&C Systems

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
digitalICDocumentation?: Record<SystemReference, {
  description: string;
  modelingApproach: string;
  specialConsiderations?: string;
}>;

// Also in dedicated interface
export interface DigitalInstrumentationAndControl extends Unique, Named {
  systemReference: SystemReference;
  description: string;
  methodology: string;
  assumptions?: string[];
  failureModes?: string[];
  specialConsiderations?: string[];
}
```

**EBR-II Example (Note: EBR-II used primarily analog I&C):**
```typescript
const processDoc: ProcessDocumentation = {
  digitalICDocumentation: {
    "SYS-PlantControlSystem": {
      description: "EBR-II utilized primarily analog instrumentation and control systems throughout its operational history. The plant control system employed analog control circuits for reactor power control, primary flow control, and balance of plant systems. The reactor protection system (RSS) was an independent analog system using redundant instrument channels.",
      modelingApproach: "Standard fault tree modeling techniques were applied to analog I&C components using established component failure rates from nuclear industry data sources. Common cause failures were modeled using the beta factor method.",
      specialConsiderations: "The reliance on analog I&C systems with simple, proven components contributed to the high reliability of the protection system. The inherent safety characteristics of EBR-II reduced reliance on I&C systems for accident mitigation."
    }
  }
};

const analogIC: DigitalInstrumentationAndControl = {
  id: "AIC-001",
  name: "EBR-II Reactor Protection System",
  systemReference: "SYS-RSS",
  description: "Analog instrumentation and control system for reactor trip functions",
  methodology: "Traditional fault tree analysis with beta factor common cause failure modeling",
  assumptions: [
    "Trip setpoints are properly calibrated per test procedures",
    "Redundant channels provide adequate protection against single failures",
    "Signal validation performed during periodic testing"
  ],
  failureModes: [
    "Failure to detect trip condition",
    "Failure to transmit trip signal",
    "Failure of trip breakers to open",
    "Spurious trip"
  ],
  specialConsiderations: [
    "EBR-II's inherent safety characteristics reduce dependence on protection system for preventing core damage during many transients"
  ]
};
```

#### (u) Passive Systems

**Schema Coverage:**
```typescript
// ProcessDocumentation interface
passiveSystemsDocumentation?: Record<SystemReference, {
  description: string;
  uncertaintyEvaluation: string;
}>;

// Also in dedicated interface
export interface PassiveSystemsTreatment extends Unique, Named {
  systemReference: SystemReference;
  description: string;
  performanceAnalysisRef?: string;
  uncertaintyAnalysis?: string;
  relevantPhysicalPhenomena?: string[];
  uncertaintyEvaluation?: string;
}
```

**EBR-II Example (Passive Safety Features):**
```typescript
const processDoc: ProcessDocumentation = {
  passiveSystemsDocumentation: {
    "SYS-PrimarySodium": {
      description: "The EBR-II primary sodium system incorporates multiple passive safety features that enable safe response to transients without requiring active systems or operator actions. Key features include: natural circulation capability upon loss of forced flow, large thermal inertia due to substantial sodium inventory, and inherent negative reactivity feedback mechanisms that reduce reactor power during temperature excursions.",
      uncertaintyEvaluation: "Uncertainties in natural circulation performance were evaluated through both experimental tests and analytical models. The 1986 loss-of-flow tests provided empirical validation of natural circulation capability. SASSYS code analyses modeled the uncertainties in flow distribution, heat transfer coefficients, and reactivity feedback parameters. These analyses demonstrated substantial margin between predicted peak temperatures and safety limits, even with conservative treatment of uncertainties."
    },
    "SYS-ShutdownCooling": {
      description: "The shutdown cooling system provides passive decay heat removal through natural circulation of NaK in closed loops and natural draft air flow through air-cooled heat exchangers. No pumps or forced circulation is required for operation.",
      uncertaintyEvaluation: "Uncertainties in natural circulation performance of the NaK loops and natural draft air flow were evaluated through engineering analyses and thermal-hydraulic models. Parameters varied included heat transfer coefficients, flow resistance factors, and ambient air conditions. Results demonstrated that even with conservative treatment of uncertainties, the system maintained temperatures below safety limits."
    }
  }
};

const passiveSystems: PassiveSystemsTreatment[] = [
  {
    id: "PST-ReactivityFeedback-001",
    name: "EBR-II Inherent Reactivity Feedback",
    systemReference: "SYS-Core",
    description: "EBR-II's core design incorporates strong negative reactivity feedback mechanisms that inherently limit power excursions without requiring control rod movement or other active interventions. Key feedback mechanisms include thermal expansion of fuel and structural components, Doppler broadening, and sodium density effects.",
    performanceAnalysisRef: "ANL/CP-74542: 'EBR-II Inherent Safety Demonstration Tests'",
    uncertaintyAnalysis: "Monte Carlo analysis of reactivity coefficients based on measurement uncertainties",
    relevantPhysicalPhenomena: [
      "Thermal expansion of metallic fuel",
      "Axial expansion of fuel pins",
      "Radial expansion of core support structure",
      "Doppler broadening in fertile material",
      "Sodium density changes affecting neutron leakage"
    ],
    uncertaintyEvaluation: "Reactivity feedback coefficients were measured during EBR-II operation and validated during the inherent safety demonstration tests. SASSYS code analyses incorporated uncertainties in these coefficients to evaluate the robustness of the shutdown mechanism. Results demonstrated that even with coefficients reduced to 50% of their measured values, the reactor would safely shut down during ATWS events."
  },
  {
    id: "PST-NaturalCirculation-001",
    name: "EBR-II Natural Circulation Cooling",
    systemReference: "SYS-PrimarySodium",
    description: "Upon loss of forced circulation, the EBR-II primary system establishes natural circulation flow paths that maintain adequate cooling of the core. The Z-pipe configuration between the high and low pressure plena facilitates the establishment of these flow paths.",
    performanceAnalysisRef: "ANL-RDP-105: 'Results of LOFWS/LOHSWS Safety Tests'",
    uncertaintyAnalysis: "Sensitivity studies on flow resistance, buoyancy forces, and heat transfer",
    relevantPhysicalPhenomena: [
      "Thermal buoyancy",
      "Flow path resistance",
      "Heat transfer from fuel to sodium",
      "Thermal stratification in the primary tank"
    ],
    uncertaintyEvaluation: "Natural circulation performance was validated through the landmark loss-of-flow tests conducted in 1986. Analytical models using the SASSYS code incorporated uncertainties in flow resistance factors, heat transfer coefficients, and core power distribution. These analyses demonstrated that natural circulation would maintain cladding temperatures below safety limits even with conservative treatment of uncertainties."
  },
  {
    id: "PST-ThermalInertia-001",
    name: "EBR-II Primary System Thermal Inertia",
    systemReference: "SYS-PrimarySodium",
    description: "The large sodium inventory (approximately 86,000 gallons) in the EBR-II primary tank provides substantial thermal inertia that slows temperature rises during transients, allowing time for passive safety features to respond or operator actions to be taken.",
    performanceAnalysisRef: "ANL-7323: 'Hazard Summary Report for EBR-II'",
    uncertaintyAnalysis: "Thermal transient analyses with varied heat capacity parameters",
    relevantPhysicalPhenomena: [
      "Sodium heat capacity",
      "Thermal mixing in primary tank",
      "Heat transfer to structural components",
      "Heat loss through primary tank insulation"
    ],
    uncertaintyEvaluation: "Thermal transient analyses using the SASSYS code incorporated uncertainties in sodium heat capacity, mixing effects, and heat losses. These analyses demonstrated that the large thermal inertia provides extended time windows (hours to days) for response to loss of heat sink events, significantly enhancing safety margins."
  }
];
```

### SY-C2: Model Uncertainty Documentation

The schema includes the `ModelUncertaintyDocumentation` interface that extends `BaseModelUncertaintyDocumentation` to address SY-C2 requirements explicitly for EBR-II:

```typescript
export interface ModelUncertaintyDocumentation extends BaseModelUncertaintyDocumentation {
  systemSpecificUncertainties?: Record<SystemReference, {
    uncertainties: string[];
    impact: string;
  }>;
  
  reasonableAlternatives: {
    alternative: string;
    reasonNotSelected: string;
    applicableSystems?: SystemReference[];
  }[];
}
```

**EBR-II Example (Model Uncertainty Documentation):**
```typescript
const modelUncertaintyDoc: ModelUncertaintyDocumentation = {
  systemSpecificUncertainties: {
    "SYS-PrimarySodium": {
      uncertainties: [
        "Uncertainty in natural circulation flow rates due to limited experimental validation at certain power levels",
        "Uncertainty in transition behavior from forced to natural circulation",
        "Uncertainty in flow distribution through core assemblies during natural circulation"
      ],
      impact: "These uncertainties could affect the predicted peak fuel and cladding temperatures during unprotected loss of flow events. Sensitivity studies demonstrated that even with conservative treatment of these uncertainties, peak temperatures remain below safety limits with substantial margin."
    },
    "SYS-Core": {
      uncertainties: [
        "Uncertainty in magnitude of negative reactivity feedback coefficients",
        "Uncertainty in spatial distribution of reactivity effects",
        "Uncertainty in time constants for reactivity feedback mechanisms"
      ],
      impact: "These uncertainties could affect the predicted power reduction rate during unprotected transients. Sensitivity studies demonstrated that the inherent shutdown capability remains effective even with significant variation in feedback parameters."
    },
    "SYS-ShutdownCooling": {
      uncertainties: [
        "Uncertainty in natural draft air flow rates through shutdown coolers",
        "Uncertainty in NaK-to-air heat transfer coefficients",
        "Uncertainty in thermal stratification effects in primary sodium"
      ],
      impact: "These uncertainties could affect the predicted heat removal capacity of the shutdown cooling system. Sensitivity studies demonstrated adequate performance even with conservative assumptions for key parameters."
    }
  },
  
  reasonableAlternatives: [
    {
      alternative: "Explicit modeling of individual fuel assemblies with detailed flow distribution",
      reasonNotSelected: "This approach would significantly increase model complexity without substantially improving predictive capability. The lumped parameter approach with appropriate conservatism was deemed adequate given the large safety margins demonstrated in EBR-II safety tests.",
      applicableSystems: ["SYS-Core", "SYS-PrimarySodium"]
    },
    {
      alternative: "Detailed CFD modeling of natural circulation flow patterns",
      reasonNotSelected: "While CFD modeling could provide more detailed spatial distribution of flow and temperature, the lumped parameter approach in SASSYS code had been validated against experimental data from EBR-II and was deemed sufficient for PRA purposes given the margins demonstrated in safety tests.",
      applicableSystems: ["SYS-PrimarySodium"]
    },
    {
      alternative: "Detailed modeling of reactivity feedback spatial effects",
      reasonNotSelected: "The point kinetics model with appropriately averaged reactivity coefficients was deemed adequate based on validation against experimental data. The spatial effects were indirectly accounted for in the uncertainty ranges applied to reactivity coefficients.",
      applicableSystems: ["SYS-Core"]
    }
  ]
};
```

### SY-C3: Pre-Operational Assumptions Documentation

The schema leverages the base `PreOperationalAssumption` interface type which is imported from the core documentation module:

```typescript
import { 
    // ... other imports
    BasePreOperationalAssumptionsDocumentation,
    PreOperationalAssumption
} from "../core/documentation";

export interface PreOperationalAssumptionsDocumentation extends BasePreOperationalAssumptionsDocumentation {
  assumptions: PreOperationalAssumption[];
  designConstructionErrors?: string[];
}
```

While EBR-II was an operational reactor with significant operating history, this interface can be used to document assumptions made in the PRA regarding idealized design and construction. This is particularly relevant for capturing assumptions about the potential for undiscovered design or construction errors that could affect passive safety features.

**EBR-II Example (Pre-Operational Assumptions):**
```typescript
const preOpAssumptions: PreOperationalAssumptionsDocumentation = {
  assumptions: [
    {
      id: "POA-001",
      description: "The reactor core and primary system are free from undiscovered design or construction errors that would significantly impact natural circulation capability.",
      basis: "Extensive testing during commissioning and subsequent operation has validated natural circulation performance. The 1986 inherent safety demonstration tests provided direct validation of this capability."
    },
    {
      id: "POA-002",
      description: "The negative reactivity feedback mechanisms function as designed and are not compromised by any undiscovered design or manufacturing defects.",
      basis: "Reactivity coefficients were measured during reactor operation and validated during the inherent safety demonstration tests. Long-term operation did not reveal any anomalies in reactivity feedback behavior."
    },
    {
      id: "POA-003",
      description: "The Z-pipe configuration between high and low pressure plena is free from undiscovered obstructions that would impair natural circulation flow paths.",
      basis: "Flow testing during commissioning and subsequent operational data have confirmed expected flow characteristics. The loss-of-flow tests in 1986 provided direct validation of the Z-pipe function during natural circulation conditions."
    }
  ],
  designConstructionErrors: [
    "While the PRA generally assumes the absence of significant design and construction errors based on operational validation, sensitivity studies were performed to assess the impact of potential flow restrictions in natural circulation paths. These studies demonstrated that even with substantial flow restriction, the large thermal inertia and multiple heat removal paths would prevent core damage."
  ]
};
```

## Implementation Examples

This section provides more detailed examples of how the schema enables thorough documentation of key EBR-II systems and their unique passive safety features.

### EBR-II Passive Safety Features

The passive safety features of EBR-II represent one of its most distinctive and important characteristics. The schema provides dedicated interfaces for documenting these features and their performance:

```typescript
const passiveSafetyImplementation: PassiveSystemsTreatment = {
  id: "PSF-EBR-II-001",
  name: "EBR-II Integrated Passive Safety System",
  systemReference: "SYS-Integrated",
  description: "EBR-II incorporated a suite of complementary passive safety features that work together to prevent core damage during transients without requiring active system intervention or operator action. This inherent safety was demonstrated in landmark tests conducted in 1986, including the loss-of-flow and loss-of-heat-sink tests.",
  performanceAnalysisRef: "ANL/CP-74542: 'EBR-II Inherent Safety Demonstration Tests'",
  relevantPhysicalPhenomena: [
    "Negative temperature coefficient of reactivity",
    "Natural circulation of primary sodium",
    "Large thermal inertia of primary sodium pool",
    "High thermal conductivity of metallic fuel",
    "Thermal expansion of core structures"
  ],
  uncertaintyAnalysis: "Comprehensive analysis of passive safety performance was conducted using the SASSYS code, incorporating uncertainties in reactivity coefficients, natural circulation parameters, and heat transfer characteristics.",
  uncertaintyEvaluation: "The analysis demonstrated that EBR-II's passive safety features maintain core temperatures within safe limits even with conservative treatment of uncertainties. This robust performance is due to the complementary and diverse nature of the passive features, which provide defense-in-depth without relying on active systems."
};

const reactivityFeedback: SystemDefinition = {
  id: "SYS-ReactivityFeedback",
  name: "EBR-II Reactivity Feedback System",
  description: "The EBR-II core design incorporates strong negative reactivity feedback mechanisms that inherently limit power excursions without requiring control rod movement. These include feedback from fuel expansion, structural expansion, and sodium density changes.",
  boundaries: [
    "Reactor core including fuel assemblies",
    "Core support structure",
    "Control rod drivelines",
    "Primary sodium within core region"
  ],
  successCriteria: "Provide sufficient negative reactivity feedback to reduce reactor power to match available heat removal capacity during transients without exceeding temperature limits.",
  modelAssumptions: [
    "Negative reactivity feedback coefficients remain effective across the operating temperature range",
    "Fuel expansion provides prompt negative feedback during power transients",
    "The small core size enhances the effectiveness of leakage-related feedback mechanisms"
  ],
  modeledComponentsAndFailures: {
    "Core-Feedback": {
      failureModes: ["INSUFFICIENT_NEGATIVE_FEEDBACK"],
      justificationForInclusion: "Critical for inherent shutdown capability during unprotected transients"
    }
  }
};
```

### EBR-II Shutdown Cooling System

The shutdown cooling system represents a key passive heat removal mechanism in EBR-II. The schema enables detailed documentation of this system and its performance:

```typescript
const shutdownCoolingSystem: SystemDefinition = {
  id: "SYS-ShutdownCooling",
  name: "EBR-II Shutdown Cooling System",
  description: "The Shutdown Cooling System is a passive system designed to remove decay heat from the primary sodium when the normal heat removal path through the secondary sodium system is unavailable. It consists of NaK-filled loops operating by natural circulation, with heat ultimately rejected to the atmosphere through air-cooled heat exchangers located in the reactor building stack.",
  boundaries: [
    "Bayonet heat exchangers immersed in the primary sodium pool",
    "NaK-filled natural circulation loops",
    "Air-cooled heat exchangers",
    "Stack exhaust system including dampers and chimney",
    "NaK expansion tanks and instrumentation"
  ],
  successCriteria: "Remove sufficient decay heat to maintain primary sodium temperature below 550°C following reactor shutdown when the normal heat removal path is unavailable.",
  missionTime: "30 days (for long-term cooling capability)",
  schematic: {
    reference: "Drawing EBR-II-SCS-001",
    description: "Schematic showing NaK-filled loops, heat exchangers, and air flow paths"
  },
  operabilityConsiderations: [
    {
      component: "Bayonet Heat Exchanger",
      calculationRef: "CALC-EBR-SCS-001",
      notes: "Heat exchanger capacity sufficient for decay heat levels 1 hour after shutdown"
    },
    {
      component: "Air Dampers",
      calculationRef: "CALC-EBR-SCS-002",
      notes: "Dampers designed to fail open to ensure cooling capability"
    }
  ],
  operationalHistory: [
    "The shutdown cooling system has been tested periodically throughout EBR-II operation to verify natural circulation capability",
    "System performance has been consistent with design expectations"
  ],
  humanActionsForOperation: [
    {
      actionRef: "HRA-SCS-001",
      description: "Operator verification of air damper positions following system actuation"
    }
  ],
  testAndMaintenance: [
    "Quarterly verification of air damper operation",
    "Annual inspection of NaK expansion tanks and level indicators",
    "Periodic thermal performance testing of shutdown coolers"
  ],
  modelAssumptions: [
    "NaK natural circulation establishes automatically upon sensing elevated primary sodium temperatures",
    "Air flow through shutdown coolers is driven by natural draft through the stack",
    "Heat removal capacity is sufficient for decay heat levels 1 hour after shutdown",
    "Air dampers fail in the open position, ensuring cooling capability even with loss of power"
  ],
  spatialInformation: [
    {
      location: "Primary Tank",
      hazards: ["High temperature sodium", "Radiation"],
      components: ["Bayonet heat exchangers"]
    },
    {
      location: "Reactor Building Stack",
      hazards: ["Elevated temperatures", "Limited accessibility"],
      components: ["Air-cooled heat exchangers", "Air dampers", "Stack chimney"]
    }
  ],
  modeledComponentsAndFailures: {
    "Bayonet-HX-01": {
      failureModes: ["FAILURE_TO_TRANSFER_HEAT", "NAK_LEAKAGE"],
      justificationForInclusion: "Critical component for heat transfer from primary sodium to NaK loop"
    },
    "Air-HX-01": {
      failureModes: ["FAILURE_TO_TRANSFER_HEAT", "NAK_LEAKAGE"],
      justificationForInclusion: "Critical component for heat rejection to atmosphere"
    },
    "Air-Damper-01": {
      failureModes: ["FAILURE_TO_OPEN", "FAILURE_TO_REMAIN_OPEN"],
      justificationForInclusion: "Required for establishing natural draft air flow"
    }
  },
  justificationForExclusionOfComponents: [
    "NaK loop instrumentation excluded due to minimal impact on passive operation"
  ],
  justificationForExclusionOfFailureModes: [
    "Partial degradation of heat transfer excluded due to substantial design margins"
  ]
};
```

### EBR-II Primary Sodium System

The primary sodium system is the central cooling system for EBR-II and incorporates many of the passive safety features. The schema enables comprehensive documentation of this system:

```typescript
const primarySodiumSystem: SystemDefinition = {
  id: "SYS-PrimarySodium",
  name: "EBR-II Primary Sodium System",
  description: "The EBR-II Primary Sodium System circulates liquid sodium through the reactor core to remove heat during normal operation and accident conditions. The system is characterized by its capability to establish natural circulation flow paths upon loss of forced flow, a key passive safety feature. The system includes the reactor vessel, primary sodium pumps, intermediate heat exchanger (IHX), and associated piping, all contained within the primary tank.",
  boundaries: [
    "Reactor vessel including high and low pressure plena",
    "Z-pipe configuration between plena",
    "Primary sodium pumps (2)",
    "Intermediate heat exchanger (primary side)",
    "Primary tank containing bulk sodium",
    "Primary sodium piping",
    "Excludes secondary sodium system boundary"
  ],
  successCriteria: "Maintain adequate cooling of the core during normal operation and accident conditions. During loss of forced flow events, establish natural circulation sufficient to maintain peak cladding temperatures below 650°C.",
  missionTime: "Normal operation plus 72 hours for accident response",
  schematic: {
    reference: "Drawing EBR-II-PCS-001",
    description: "Primary system flow diagram showing reactor vessel, pumps, IHX, and Z-pipe configuration"
  },
  operabilityConsiderations: [
    {
      component: "Primary Sodium Pump P-1",
      calculationRef: "CALC-EBR-PCS-001",
      notes: "Pump coastdown characteristics provide sufficient momentum to establish natural circulation flow"
    },
    {
      component: "Z-Pipe Configuration",
      calculationRef: "CALC-EBR-PCS-002",
      notes: "Design ensures reliable natural circulation flow path even with loss of forced flow"
    },
    {
      component: "Intermediate Heat Exchanger",
      calculationRef: "CALC-EBR-PCS-003",
      notes: "Design allows for decay heat removal through secondary system or through shutdown coolers"
    }
  ],
  operationalHistory: [
    "The primary sodium system operated reliably throughout EBR-II's 30-year operational history",
    "The 1986 loss-of-flow test demonstrated successful transition to natural circulation cooling without scram",
    "No significant sodium leaks occurred from the primary tank during operation"
  ],
  humanActionsForOperation: [
    {
      actionRef: "HRA-PCS-001",
      description: "Operator monitoring of primary system temperatures and flows during normal operation"
    },
    {
      actionRef: "HRA-PCS-002",
      description: "Operator response to pump trip alarms (verification of natural circulation establishment)"
    }
  ],
  testAndMaintenance: [
    "Regular testing of primary pump performance",
    "Periodic calibration of flow and temperature instrumentation",
    "Visual inspection of accessible components during refueling outages"
  ],
  modelAssumptions: [
    "Natural circulation flow establishes within 60 seconds following loss of forced flow",
    "The Z-pipe configuration provides reliable flow path for natural circulation",
    "The large sodium inventory provides substantial thermal inertia during transients",
    "Negative reactivity feedback reduces power to match available cooling during unprotected transients"
  ],
  spatialInformation: [
    {
      location: "Primary Tank",
      hazards: ["High temperature sodium", "Radiation"],
      components: ["Reactor vessel", "Primary pumps", "IHX", "Z-pipe"]
    },
    {
      location: "Primary Pump Motor Area",
      hazards: ["Radiation", "Electrical hazards"],
      components: ["Primary pump motors", "Flow instrumentation"]
    }
  ],
  modeledComponentsAndFailures: {
    "PrimaryPump-01": {
      failureModes: ["FAILURE_TO_RUN", "FAILURE_TO_COASTDOWN_PROPERLY"],
      justificationForInclusion: "Critical for forced circulation and transition to natural circulation",
      componentGroup: "Pumps"
    },
    "IHX-01": {
      failureModes: ["FAILURE_TO_TRANSFER_HEAT", "EXTERNAL_LEAKAGE"],
      justificationForInclusion: "Critical for heat transfer to secondary system",
      componentGroup: "HeatExchangers"
    },
    "Z-Pipe-01": {
      failureModes: ["FLOW_BLOCKAGE"],
      justificationForInclusion: "Critical for establishing natural circulation flow path",
      componentGroup: "Piping"
    }
  },
  justificationForExclusionOfComponents: [
    "Small bore instrumentation lines excluded due to minimal impact on system function",
    "Reactor vessel cover gas system components excluded as they do not directly impact cooling function"
  ]
};
```

### EBR-II Reactor Shutdown System

The Reactor Shutdown System (RSS) provides the active protection function for EBR-II, though the passive safety features significantly reduce reliance on this system:

```typescript
const reactorShutdownSystem: SystemDefinition = {
  id: "SYS-RSS",
  name: "EBR-II Reactor Shutdown System",
  description: "The Reactor Shutdown System (RSS) provides the capability to rapidly terminate the fission reaction by inserting control and safety rods into the core. While EBR-II's passive safety features can mitigate many transients without scram, the RSS provides the primary active protection function for the reactor.",
  boundaries: [
    "Neutron flux monitoring instrumentation",
    "Temperature and flow instrumentation",
    "Reactor trip logic circuits",
    "Trip breakers",
    "Control and safety rod drive mechanisms",
    "Control and safety rods",
    "Manual scram capability"
  ],
  successCriteria: "Insert sufficient negative reactivity to shut down the reactor and maintain subcriticality following detection of off-normal conditions.",
  missionTime: "Rapid shutdown within seconds plus maintaining subcriticality indefinitely",
  schematic: {
    reference: "Drawing EBR-II-RSS-001",
    description: "RSS schematic showing instrumentation, logic circuits, and rod drive mechanisms"
  },
  operabilityConsiderations: [
    {
      component: "Safety Rod Drive Mechanism",
      calculationRef: "CALC-EBR-RSS-001",
      notes: "Design ensures rapid insertion upon trip signal or loss of power"
    },
    {
      component: "Neutron Flux Monitors",
      calculationRef: "CALC-EBR-RSS-002",
      notes: "Designed to provide reliable trip function across operating range"
    }
  ],
  operationalHistory: [
    "The RSS functioned reliably throughout EBR-II's operational history",
    "Two spurious trips occurred in 1985 due to instrumentation drift",
    "The system was not required to actuate during the 1986 inherent safety demonstration tests, highlighting the effectiveness of passive safety features"
  ],
  humanActionsForOperation: [
    {
      actionRef: "HRA-RSS-001",
      description: "Operator manual scram capability for conditions not automatically detected"
    },
    {
      actionRef: "HRA-RSS-002",
      description: "Operator response to reactor trip alarms (verification of rod insertion)"
    }
  ],
  testAndMaintenance: [
    "Monthly testing of trip logic circuits",
    "Quarterly calibration of neutron flux instrumentation",
    "Annual measurement of safety rod drop times",
    "Testing of manual scram circuits during each refueling outage"
  ],
  modelAssumptions: [
    "Trip breakers fail in the tripped position upon loss of power (fail-safe design)",
    "Safety rod insertion is gravity-driven and not dependent on external power",
    "Redundant instrumentation channels provide protection against single failures",
    "For unprotected transient analysis, the RSS is assumed to fail completely to demonstrate passive safety margins"
  ],
  spatialInformation: [
    {
      location: "Control Room",
      hazards: ["Electrical"],
      components: ["Trip logic cabinets", "Manual scram controls", "Instrumentation readouts"]
    },
    {
      location: "Reactor Top",
      hazards: ["Radiation", "Moving equipment"],
      components: ["Control and safety rod drive mechanisms"]
    },
    {
      location: "Reactor Core",
      hazards: ["High radiation", "High temperature"],
      components: ["Control and safety rods"]
    }
  ],
  modeledComponentsAndFailures: {
    "NeutronFluxMonitor-01": {
      failureModes: ["FAILURE_TO_DETECT_HIGH_FLUX", "SPURIOUS_SIGNAL"],
      justificationForInclusion: "Critical for detecting off-normal conditions",
      componentGroup: "Instrumentation"
    },
    "TripBreaker-01": {
      failureModes: ["FAILURE_TO_OPEN", "SPURIOUS_OPENING"],
      justificationForInclusion: "Critical for de-energizing rod drive mechanisms",
      componentGroup: "Electrical"
    },
    "SafetyRod-01": {
      failureModes: ["FAILURE_TO_INSERT", "MECHANICAL_BINDING"],
      justificationForInclusion: "Critical for inserting negative reactivity",
      componentGroup: "Mechanical"
    }
  },
  justificationForExclusionOfComponents: [
    "Limit switches and position indicators excluded as they do not affect the trip function",
    "Rod worth measurement equipment excluded as it does not affect emergency shutdown capability"
  ]
};
```

## Requirements Coverage Summary

The following table provides a condensed summary of how the TypeScript schema enables comprehensive compliance with regulatory requirements for the EBR-II reactor:

| Requirement | Schema Implementation | EBR-II Application |
|-------------|----------------------|-------------------|
| **SY-C1** | `ProcessDocumentation` interface with 21 specialized properties | ✅ Enables thorough documentation of EBR-II's unique systems, particularly its passive safety features |
| **SY-C2** | `ModelUncertaintyDocumentation` interface | ✅ Facilitates documentation of uncertainties in natural circulation, reactivity feedback, and other parameters specific to EBR-II |
| **SY-C3** | `PreOperationalAssumptionsDocumentation` interface | ✅ Although EBR-II has significant operational history, this interface captures assumptions about potential undiscovered errors |
| **Passive Safety** | `PassiveSystemsTreatment` interface | ✅ Specially designed to document EBR-II's key passive safety features, including negative reactivity feedback and natural circulation |
| **System Dependencies** | `SystemDependency` interface | ✅ Captures the relatively simple dependency structure of EBR-II systems, particularly the reduced dependencies for passive features |
| **Uncertainty Analysis** | Dedicated uncertainty interfaces | ✅ Enables documentation of SASSYS code analyses and experimental validation of EBR-II passive safety features |

### SY-C1 Documentation Structure for EBR-II

The `ProcessDocumentation` interface comprehensively addresses all 21 sub-requirements of SY-C1 for EBR-II:

| Documentation Category | SY-C1 Requirements | Interface Properties | EBR-II Application |
|------------------------|-------------------|---------------------|-------------------|
| System Definition | (a)(b)(c)(f) | `systemFunctionDocumentation`, `systemBoundaryDocumentation`, `systemSchematicReferences`, `successCriteriaDocumentation` | Captures the unique functions of EBR-II systems, particularly their passive safety roles |
| Operational Aspects | (d)(e)(g)(h) | `equipmentOperabilityDocumentation`, `operationalHistoryDocumentation`, `humanActionsDocumentation`, `testMaintenanceProceduresDocumentation` | Documents EBR-II's operational history, including the landmark 1986 tests demonstrating inherent safety |
| Model Structure | (i)(j)(k)(l)(m)(n)(s) | `dependencySearchDocumentation`, `spatialInformationDocumentation`, `modelingAssumptionsDocumentation`, etc. | Enables documentation of modeling assumptions specific to EBR-II's passive features |
| Analysis Results | (o)(p)(q)(r) | `evaluationResultsDocumentation`, `sensitivityStudiesDocumentation`, etc. | Captures results of SASSYS analyses and sensitivity studies on reactivity feedback and natural circulation |
| Special Systems | (t)(u) | `digitalICDocumentation`, `passiveSystemsDocumentation` | Particularly the `passiveSystemsDocumentation` property is crucial for documenting EBR-II's unique inherent safety features |
