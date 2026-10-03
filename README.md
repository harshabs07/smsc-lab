;===============================================================
; 2-D PN JUNCTION DIODE
;===============================================================
;
; Dimensions:
; X = 1 um
; Y = 1 um
;
; P region = 1e18 cm^-3
; N region = 1e18 cm^-3
;
; PN junction at Y = 0.5 um
;
; Coordinate system:
; X: 0 to 1 um
; Y: 0 to 1 um
; Z: 0
;
;===============================================================

;===============================================================
; GEOMETRY
;===============================================================

;-----------------------------
; P-type Silicon region
;-----------------------------

(sdegeo:create-rectangle
  (position 0.0 0.0 0.0)
  (position 1.0 0.5 0.0)
  "Silicon"
  "P_Silicon"
)

;-----------------------------
; N-type Silicon region
;-----------------------------

(sdegeo:create-rectangle
  (position 0.0 0.5 0.0)
  (position 1.0 1.0 0.0)
  "Silicon"
  "N_Silicon"
)

;===============================================================
; CONTACT DEFINITION
;===============================================================

;-----------------------------
; P contact
;-----------------------------

(sdegeo:define-contact-set
  "p_contact"
  4
  (color:rgb 1 0 0)
  "##"
)

;-----------------------------
; N contact
;-----------------------------

(sdegeo:define-contact-set
  "n_contact"
  4
  (color:rgb 0 0 1)
  "##"
)

;===============================================================
; CONTACT ASSIGNMENT
;===============================================================

;-----------------------------
; Bottom P contact
;-----------------------------
;
; The bottom edge is located at Y = 0.0 um.
; Pick a point in the middle of that edge.
;

(sdegeo:set-contact
  (find-edge-id
    (position 0.5 0.0 0.0)
  )
  "p_contact"
)

;-----------------------------
; Top N contact
;-----------------------------
;
; The top edge is located at Y = 1.0 um.
;

(sdegeo:set-contact
  (find-edge-id
    (position 0.5 1.0 0.0)
  )
  "n_contact"
)

;===============================================================
; DOPING
;===============================================================

;-----------------------------
; P-type doping
;-----------------------------

(sdedr:define-constant-profile
  "P_Doping"
  "BoronActiveConcentration"
  1e18
)

(sdedr:define-constant-profile-region
  "Place_P_Doping"
  "P_Doping"
  "P_Silicon"
)

;-----------------------------
; N-type doping
;-----------------------------

(sdedr:define-constant-profile
  "N_Doping"
  "PhosphorusActiveConcentration"
  1e18
)

(sdedr:define-constant-profile-region
  "Place_N_Doping"
  "N_Doping"
  "N_Silicon"
)

;===============================================================
; MESH REFINEMENT
;===============================================================

;-----------------------------
; Global mesh refinement
;-----------------------------

(sdedr:define-refinement-size
  "Global_Refinement"
  0.10
  0.10
  0.005
  0.005
)

; Apply global refinement to silicon

(sdedr:define-refinement-material
  "Global_Placement"
  "Global_Refinement"
  "Silicon"
)

;===============================================================
; JUNCTION REFINEMENT WINDOW
;===============================================================

; PN junction is located at Y = 0.5 um.
;
; Refinement window:
; X = 0 to 1 um
; Y = 0.45 to 0.55 um
;

(sdedr:define-refeval-window
  "Junction_Window"
  "Rectangle"
  (position 0.0 0.45 0.0)
  (position 1.0 0.55 0.0)
)

;-----------------------------
; Fine mesh near junction
;-----------------------------

(sdedr:define-refinement-size
  "Junction_Refinement"
  0.02
  0.005
  0.001
  0.001
)

; Apply refinement to junction window

(sdedr:define-refinement-placement
  "Junction_Placement"
  "Junction_Refinement"
  "Junction_Window"
)

;===============================================================
; DOPING GRADIENT REFINEMENT
;===============================================================

(sdedr:define-refinement-function
  "Junction_Refinement"
  "DopingConcentration"
  "MaxTransDiff"
  1
)

;===============================================================
; MESH GENERATION
;===============================================================

(sde:build-mesh "PN_Diode")

;===============================================================
; END OF FILE
;===============================================================