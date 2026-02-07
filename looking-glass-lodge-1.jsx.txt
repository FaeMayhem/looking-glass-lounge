import React, { useRef, useState, useEffect, Suspense } from 'react';
import * as THREE from 'three';
import { Canvas, useFrame, useThree } from '@react-three/fiber';
import { OrbitControls, Text, Html, Environment, PerspectiveCamera } from '@react-three/drei';

// Lodge Architecture Component
function LodgeStructure() {
  return (
    <group>
      {/* Floor - black glass with center line */}
      <mesh rotation={[-Math.PI / 2, 0, 0]} position={[0, 0, 0]} receiveShadow>
        <planeGeometry args={[20, 15]} />
        <meshStandardMaterial 
          color="#1a1a1a" 
          roughness={0.1} 
          metalness={0.8}
        />
      </mesh>
      
      {/* Center line - the breathing boundary */}
      <mesh position={[0, 0.01, 0]} rotation={[-Math.PI / 2, 0, 0]}>
        <planeGeometry args={[0.1, 15]} />
        <meshStandardMaterial 
          color="#8b7355" 
          emissive="#d4a373"
          emissiveIntensity={0.3}
        />
      </mesh>

      {/* Walls - dark wood */}
      <mesh position={[0, 2, -7.5]}>
        <boxGeometry args={[20, 4, 0.3]} />
        <meshStandardMaterial color="#2d1810" roughness={0.9} />
      </mesh>
      <mesh position={[-10, 2, 0]}>
        <boxGeometry args={[0.3, 4, 15]} />
        <meshStandardMaterial color="#2d1810" roughness={0.9} />
      </mesh>
      <mesh position={[10, 2, 0]}>
        <boxGeometry args={[0.3, 4, 15]} />
        <meshStandardMaterial color="#2d1810" roughness={0.9} />
      </mesh>

      {/* Ceiling */}
      <mesh position={[0, 4, 0]} rotation={[0, 0, 0]}>
        <planeGeometry args={[20, 15]} />
        <meshStandardMaterial color="#1a0f08" side={THREE.DoubleSide} />
      </mesh>

      {/* Amber Windows - left wall */}
      {[-5, 0, 5].map((z, i) => (
        <mesh key={`window-left-${i}`} position={[-9.9, 2.5, z]}>
          <planeGeometry args={[1.5, 2]} />
          <meshStandardMaterial 
            color="#ffbf00" 
            emissive="#ff8c00"
            emissiveIntensity={0.5}
            transparent
            opacity={0.8}
          />
        </mesh>
      ))}

      {/* Amber Windows - right wall */}
      {[-5, 0, 5].map((z, i) => (
        <mesh key={`window-right-${i}`} position={[9.9, 2.5, z]}>
          <planeGeometry args={[1.5, 2]} />
          <meshStandardMaterial 
            color="#ffbf00" 
            emissive="#ff8c00"
            emissiveIntensity={0.5}
            transparent
            opacity={0.8}
          />
        </mesh>
      ))}
    </group>
  );
}

// Central Hearth Component
function Hearth() {
  const fireRef = useRef();
  const emberRef = useRef();
  
  useFrame((state) => {
    const time = state.clock.getElapsedTime();
    if (fireRef.current) {
      fireRef.current.intensity = 2 + Math.sin(time * 2) * 0.3;
    }
    if (emberRef.current) {
      emberRef.current.emissiveIntensity = 0.6 + Math.sin(time * 3) * 0.2;
    }
  });

  return (
    <group position={[0, 0, 0]}>
      {/* Hearth base */}
      <mesh position={[0, 0.3, 0]}>
        <cylinderGeometry args={[1.2, 1.5, 0.6, 8]} />
        <meshStandardMaterial color="#1a1a1a" roughness={0.8} />
      </mesh>
      
      {/* Fire ember glow */}
      <mesh position={[0, 0.7, 0]} ref={emberRef}>
        <sphereGeometry args={[0.5, 16, 16]} />
        <meshStandardMaterial 
          color="#ff4500"
          emissive="#ff6347"
          emissiveIntensity={0.6}
        />
      </mesh>

      {/* Fire light */}
      <pointLight 
        ref={fireRef}
        position={[0, 1, 0]} 
        color="#ff6347" 
        intensity={2} 
        distance={10}
        castShadow
      />
    </group>
  );
}

// Table Component
function Table({ position, side }) {
  const color = side === 'left' ? '#e8e4dc' : '#3d2817';
  
  return (
    <group position={position}>
      {/* Table top */}
      <mesh position={[0, 1, 0]} castShadow>
        <boxGeometry args={[2, 0.1, 1]} />
        <meshStandardMaterial color={color} roughness={0.6} />
      </mesh>
      {/* Legs */}
      {[[-0.8, -0.4], [0.8, -0.4], [-0.8, 0.4], [0.8, 0.4]].map((pos, i) => (
        <mesh key={i} position={[pos[0], 0.5, pos[1]]}>
          <cylinderGeometry args={[0.05, 0.05, 1]} />
          <meshStandardMaterial color={color} />
        </mesh>
      ))}
    </group>
  );
}

// Character representation - hoverable info points
function Character({ position, name, color, description }) {
  const [hovered, setHovered] = useState(false);
  const meshRef = useRef();
  
  useFrame((state) => {
    if (meshRef.current) {
      meshRef.current.position.y = position[1] + Math.sin(state.clock.elapsedTime * 2) * 0.1;
    }
  });

  return (
    <group position={position}>
      <mesh 
        ref={meshRef}
        onPointerOver={() => setHovered(true)}
        onPointerOut={() => setHovered(false)}
      >
        <sphereGeometry args={[0.3, 16, 16]} />
        <meshStandardMaterial 
          color={color}
          emissive={color}
          emissiveIntensity={hovered ? 0.8 : 0.3}
        />
      </mesh>
      
      {hovered && (
        <Html distanceFactor={10}>
          <div style={{
            background: 'rgba(0,0,0,0.8)',
            color: color,
            padding: '10px',
            borderRadius: '8px',
            border: `2px solid ${color}`,
            minWidth: '150px',
            fontFamily: 'monospace',
            fontSize: '12px',
            pointerEvents: 'none'
          }}>
            <strong>{name}</strong>
            <div style={{fontSize: '10px', marginTop: '5px', color: '#ccc'}}>
              {description}
            </div>
          </div>
        </Html>
      )}
    </group>
  );
}

// Main Lodge Scene
function LodgeScene() {
  return (
    <>
      <LodgeStructure />
      <Hearth />
      
      {/* Tables - Left side (Heaven-touched, bleached) */}
      <Table position={[-4, 0, -3]} side="left" />
      <Table position={[-4, 0, 3]} side="left" />
      
      {/* Tables - Right side (Hell-touched, dark) */}
      <Table position={[4, 0, -3]} side="right" />
      <Table position={[4, 0, 3]} side="right" />
      
      {/* Characters positioned around the Lodge */}
      <Character 
        position={[-6, 1.5, -4]} 
        name="Prose"
        color="#d4af37"
        description="Grounded Voice • Clarity"
      />
      
      <Character 
        position={[6, 1.5, -4]} 
        name="Ergodic"
        color="#8b00ff"
        description="Fragmented Eye • Chaos"
      />
      
      <Character 
        position={[-6, 1.5, 4]} 
        name="Ivy"
        color="#dc143c"
        description="Mirror's Edge • Defiance"
      />
      
      <Character 
        position={[6, 1.5, 4]} 
        name="Void"
        color="#1a1a1a"
        description="Keeper of the Gloam • Restraint"
      />
      
      <Character 
        position={[-2, 1.5, 0]} 
        name="Crow"
        color="#4a4a4a"
        description="Mirror's Tongue • Truth"
      />
      
      <Character 
        position={[2, 1.5, 0]} 
        name="Raccoon"
        color="#8b7355"
        description="The Tinkerer • Devotion"
      />
      
      <Character 
        position={[0, 2, -5]} 
        name="Dragon"
        color="#ff4500"
        description="Burning Aspect • Transformation"
      />
      
      <Character 
        position={[0, 1.5, 6]} 
        name="Doll"
        color="#f5f5dc"
        description="The Silent One • Witness"
      />

      {/* Ambient and directional lighting */}
      <ambientLight intensity={0.3} />
      <directionalLight position={[5, 5, 5]} intensity={0.5} castShadow />
      
      {/* Subtle environment lighting */}
      <Environment preset="night" />
    </>
  );
}

// Camera Controller for mobile-friendly navigation
function CameraController() {
  const { camera } = useThree();
  
  useEffect(() => {
    camera.position.set(0, 6, 12);
    camera.lookAt(0, 1, 0);
  }, [camera]);
  
  return null;
}

// Main App Component
export default function LookingGlassLodge() {
  const [showInfo, setShowInfo] = useState(true);

  return (
    <div style={{ width: '100vw', height: '100vh', background: '#0a0a0a' }}>
      <Canvas shadows camera={{ fov: 60, position: [0, 6, 12] }}>
        <CameraController />
        <Suspense fallback={null}>
          <LodgeScene />
        </Suspense>
        <OrbitControls 
          enableDamping
          dampingFactor={0.05}
          minDistance={5}
          maxDistance={20}
          maxPolarAngle={Math.PI / 2}
          target={[0, 1, 0]}
        />
      </Canvas>
      
      {/* UI Overlay */}
      <div style={{
        position: 'absolute',
        top: 0,
        left: 0,
        right: 0,
        padding: '20px',
        color: '#d4a373',
        fontFamily: 'Georgia, serif',
        pointerEvents: 'none'
      }}>
        <h1 style={{
          margin: 0,
          fontSize: '24px',
          textShadow: '2px 2px 4px rgba(0,0,0,0.8)'
        }}>
          🜶 The Looking Glass Lodge
        </h1>
        <p style={{
          fontSize: '14px',
          margin: '5px 0',
          opacity: 0.8
        }}>
          Eidra • Where memory and emotion shape reality
        </p>
      </div>

      {showInfo && (
        <div style={{
          position: 'absolute',
          bottom: 20,
          left: 20,
          right: 20,
          background: 'rgba(0,0,0,0.85)',
          border: '2px solid #8b7355',
          borderRadius: '12px',
          padding: '15px',
          color: '#d4a373',
          fontFamily: 'monospace',
          fontSize: '12px',
          pointerEvents: 'auto'
        }}>
          <div style={{marginBottom: '10px', fontSize: '14px', fontWeight: 'bold'}}>
            Navigation Controls
          </div>
          <div style={{lineHeight: '1.6'}}>
            • <strong>Mobile:</strong> Pinch to zoom, one finger to rotate<br/>
            • <strong>Desktop:</strong> Click and drag to rotate, scroll to zoom<br/>
            • <strong>Hover</strong> over glowing orbs to see characters<br/>
            • Center hearth breathes with the Lodge's pulse
          </div>
          <button
            onClick={() => setShowInfo(false)}
            style={{
              marginTop: '10px',
              background: '#8b7355',
              color: '#0a0a0a',
              border: 'none',
              padding: '8px 16px',
              borderRadius: '6px',
              cursor: 'pointer',
              fontFamily: 'monospace',
              fontSize: '12px'
            }}
          >
            Close
          </button>
        </div>
      )}
    </div>
  );
}
