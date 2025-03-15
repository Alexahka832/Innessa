import { useState, useEffect } from "react";
import { motion, AnimatePresence, useMotionValue, useTransform } from "framer-motion";
import { Button } from "@/components/ui/button";
import { 
  FaCrown, FaHeart, FaStar, FaGem, FaFeather, FaLeaf, 
  FaSnowflake, FaMoon, FaSun, FaCloud, FaBolt, FaRainbow, FaMusic 
} from "react-icons/fa";

const compliments = [/* оставьте ваш текущий массив комплиментов */];

const colors = ["bg-red-400", "bg-blue-400", "bg-green-400", "bg-yellow-400", 
               "bg-purple-400", "bg-pink-400", "bg-orange-400"];

const ParticleLayer = () => {
  const mouseX = useMotionValue(0);
  const mouseY = useMotionValue(0);
  
  useEffect(() => {
    const handleMove = (e) => {
      mouseX.set(e.clientX);
      mouseY.set(e.clientY);
    };
    window.addEventListener("mousemove", handleMove);
    return () => window.removeEventListener("mousemove", handleMove);
  }, []);

  return (
    <div className="absolute inset-0 pointer-events-none">
      {[...Array(50)].map((_, i) => {
        const rotate = useTransform(mouseX, [0, window.innerWidth], [-15, 15]);
        const scale = useTransform(mouseY, [0, window.innerHeight], [0.8, 1.2]);
        
        return (
          <motion.div
            key={i}
            className="absolute text-white/20"
            style={{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              rotate,
              scale,
            }}
            animate={{
              y: [0, -40, 0],
              x: [0, Math.random() * 40 - 20, 0],
              rotate: [0, Math.random() * 360],
            }}
            transition={{
              duration: 4 + Math.random() * 4,
              repeat: Infinity,
              ease: "easeInOut",
            }}
          >
            <FaStar size={24} />
          </motion.div>
        );
      })}
    </div>
  );
};

const FloatingLetters = ({ children }) => {
  return (
    <div className="relative flex">
      {children.split('').map((letter, i) => (
        <motion.span
          key={i}
          className="inline-block mx-1"
          animate={{
            y: [0, -15, 0],
            rotate: [0, Math.random() * 20 - 10, 0],
          }}
          transition={{
            duration: 3 + i * 0.2,
            repeat: Infinity,
            ease: "easeInOut",
          }}
        >
          {letter}
        </motion.span>
      ))}
    </div>
  );
};

export default function InessaApp() {
  const [compliment, setCompliment] = useState(null);
  const [bgColor, setBgColor] = useState(colors[0]);
  const [particles, setParticles] = useState([]);

  const handleCompliment = () => {
    setCompliment(compliments[Math.floor(Math.random() * compliments.length)]);
    
    // Add burst particles
    const newParticles = Array(10).fill().map((_, i) => ({
      id: Date.now() + i,
      x: Math.random() * 100,
      y: Math.random() * 100,
    }));
    setParticles([...particles, ...newParticles]);
  };

  const changeBackground = () => {
    setBgColor(colors[(colors.indexOf(bgColor) + 1) % colors.length]);
  };

  return (
    <div className={`flex flex-col items-center justify-center min-h-screen ${bgColor} p-8 relative 
                    overflow-hidden transition-colors duration-1000`}>
      
      <ParticleLayer />
      
      {/* Animated Particles Burst */}
      <AnimatePresence>
        {particles.map((particle) => (
          <motion.div
            key={particle.id}
            className="absolute text-white/50"
            initial={{ scale: 0, opacity: 1 }}
            animate={{ 
              scale: [0, 1, 0],
              x: [0, Math.random() * 200 - 100],
              y: [0, Math.random() * 200 - 100],
              rotate: Math.random() * 360,
            }}
            exit={{ opacity: 0 }}
            transition={{ duration: 1.5, ease: "easeOut" }}
            onAnimationComplete={() => setParticles(prev => prev.filter(p => p.id !== particle.id))}
          >
            <FaGem size={20} />
          </motion.div>
        ))}
      </AnimatePresence>

      {/* Floating Header */}
      <motion.div 
        className="relative mb-12"
        initial={{ scale: 0.5, opacity: 0 }}
        animate={{ scale: 1, opacity: 1 }}
        transition={{ duration: 0.8, type: "spring" }}
      >
        <div className="text-6xl font-bold font-mono text-white text-center filter drop-shadow-2xl">
          <FloatingLetters>ИННЕССА</FloatingLetters>
          <FaCrown className="absolute -top-8 left-1/2 transform -translate-x-1/2 text-amber-400 
                            animate-pulse" />
        </div>
      </motion.div>

      {/* Compliment Card */}
      <AnimatePresence>
        {compliment && (
          <motion.div
            initial={{ opacity: 0, y: 50 }}
            animate={{ opacity: 1, y: 0 }}
            exit={{ opacity: 0, y: -50 }}
            className="mb-8 max-w-2xl text-center"
          >
            <div className="bg-white/10 backdrop-blur-lg rounded-2xl p-6 shadow-2xl border-2 border-white/20">
              <p className="text-2xl font-semibold text-white italic leading-relaxed">
                "{compliment}"
              </p>
            </div>
          </motion.div>
        )}
      </AnimatePresence>

      {/* Interactive Buttons Grid */}
      <motion.div 
        className="grid gap-4 grid-cols-1 sm:grid-cols-3"
        layout
      >
        <motion.div whileHover={{ scale: 1.05 }}>
          <Button 
            onClick={handleCompliment}
            className="bg-gradient-to-br from-pink-400 to-rose-600 text-white py-6 px-8 rounded-2xl
                     text-lg font-bold shadow-xl hover:shadow-2xl transition-all duration-300"
          >
            ✨ Получить Энергию
          </Button>
        </motion.div>

        <motion.div whileHover={{ scale: 1.05 }}>
          <Button 
            onClick={changeBackground}
            className="bg-gradient-to-br from-cyan-400 to-blue-600 text-white py-6 px-8 rounded-2xl
                     text-lg font-bold shadow-xl hover:shadow-2xl transition-all duration-300"
          >
            🌈 Сменить Ауру
          </Button>
        </motion.div>

        <motion.div whileHover={{ scale: 1.05 }}>
          <Button 
            onClick={() => setCompliment(null)}
            className="bg-gradient-to-br from-emerald-400 to-green-600 text-white py-6 px-8 rounded-2xl
                     text-lg font-bold shadow-xl hover:shadow-2xl transition-all duration-300"
          >
            🍃 Очистить Пространство
          </Button>
        </motion.div>
      </motion.div>

      {/* Interactive Background Elements */}
      <div className="absolute inset-0 overflow-hidden pointer-events-none">
        {[...Array(8)].map((_, i) => (
          <motion.div
            key={i}
            className="absolute text-white/10"
            style={{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
            }}
            animate={{
              scale: [1, 1.5, 1],
              rotate: [0, 360],
              opacity: [0.1, 0.3, 0.1],
            }}
            transition={{
              duration: 10 + Math.random() * 10,
              repeat: Infinity,
              ease: "easeInOut",
            }}
          >
            <FaSnowflake size={80} />
          </motion.div>
        ))}
      </div>
    </div>
  );
}
